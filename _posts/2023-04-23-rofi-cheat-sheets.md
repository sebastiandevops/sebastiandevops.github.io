---
layout: post
title: "Rofi Cheat Sheets"
subtitle: "A Convenient Way to Access Programming Cheat Sheets."
date: 2023-04-23 06:00:13 -0400
background: '/img/posts/2023-04-23-rofi-cheat-sheets/2023-04-23_06-29.png'
---
<div class="paragraph-with-image">
  <p>If you're a programmer, you've probably encountered situations where you need to quickly look up a piece of syntax or a function. cheat.sh is a website that provides cheat sheets for many popular programming languages and tools, accessible through a simple curl command. However, typing out the full command every time you want to look something up can be tedious. That's where my tool comes in: a simple script that streamlines the process and makes it easier to access cheat.sh cheat sheets from a rofi menu.</p>

  <p>Here's how it works: the first script takes in the user input (which should be a cheat.sh request space separated) and passes it to the curl command, which retrieves the cheat sheet from cheat.sh. The output is then piped to less, allowing you to scroll through it in the terminal window. The second script sets up a rofi menu that prompts you to enter a cheat.sh request, and then calls the first script to retrieve and display the cheat sheet in a separate terminal window of your choice (in my case, I'm using kitty).</p>
  <img src="/img/posts/2023-04-23-rofi-cheat-sheets/2023-04-23_06-29.png">

  <p>Here's the code for the first script:</p>
</div>

{% highlight text %}
#!/usr/bin/env bash
i=1;
request=""
for param in "$@"
do
  if [ "$param" != "$1" ]; then
    request+="${param} ";
  fi
  i=$((i + 1));
done
request=$(echo "$request" | sed -r 's/[ ]+/+/g')
curl -s cheat.sh/"$1"/"$request" 2>&1 | \
less -RX
{% endhighlight %}

<p>Here's a line-by-line explanation of the first script:</p>

{% highlight text %}
#!/usr/bin/env bash
{% endhighlight %}
<p>This line specifies the shell to use for the script as bash. It is known as the "shebang" line and it tells the system what interpreter to use for executing the script.</p>

{% highlight text %}
i=1;
request=""
{% endhighlight %}
<p>These lines initialize two variables - <code>i</code> and <code>request</code>. <code>i</code> is initialized to 1, and <code>request</code> is initialized to an empty string.</p>
{% highlight text %}
for param in "$@"
do
{% endhighlight %}
<p>This starts a for loop that iterates over each command-line argument passed to the script.</p>

{% highlight text %}
if [ "$param" != "$1" ]; then
{% endhighlight %}
<p>This line checks if the current argument being processed is not the first argument (i.e., the programming language for which the cheat sheet is being searched). The <code>$1</code> variable refers to the first argument passed to the script.</p>

{% highlight text %}
request+="${param} ";
{% endhighlight %}
<p>If the current argument is not the first argument, it is concatenated to the request variable with a space character.</p>

{% highlight text %}
  fi
  i=$((i + 1));
done
{% endhighlight %}
<p>These lines close the if statement and increment the loop counter <code>i</code>.<code>done</code> marks the end of the for loop.</p>

{% highlight text %}
request=$(echo "$request" | sed -r 's/[ ]+/+/g')
{% endhighlight %}
<p>This line replaces any space characters in the request variable with + symbols using the sed command, and stores the result back in the request variable.</p>

{% highlight text %}
curl -s cheat.sh/"$1"/"$request" 2>&1 | \
less -RX
{% endhighlight %}
<p>The line containing the <code>curl</code> command sends an HTTP GET request to cheat.sh with the specified programming language and cheat sheet request. The <code>-s</code> flag makes the command silent, and the <code>2>&1</code> redirects error messages to standard output. The <code>|</code> symbol pipes the output of the command to the <code>less</code> command in the pipeline, which allows for scrolling through the output. The <code>-R</code> flag makes <code>less</code> display ANSI color escape sequences correctly, and the <code>-X</code> flag prevents the output from being cleared on exit.</p>

<p>Here's a line-by-line explanation of the second script:</p>

{% highlight text %}
string="$(rofi -dmenu -i -p "Cheat:" -theme ~/.config/rofi/launchers/cheat.rasi)"
{% endhighlight %}
<p>This line launches a Rofi dmenu with the prompt "Cheat: " and a custom theme from the file located at <code>~/.config/rofi/launchers/cheat.rasi</code>. The <code>$(...)</code> syntax is used to capture the output of the rofi command and store it in the variable <code>string</code>.</p>

{% highlight text %}
kitty --class kitty-helper -e $HOME/bin/cheat $(echo $string)
{% endhighlight %}
<p>This line opens a new Kitty terminal window with the class kitty-helper, which can be used for identifying this window or configuring its appearance. The <code>-e</code> option is used to execute a command in the new window. The <code>$(echo $string)</code> part is used to pass the contents of the <code>string</code> variable as arguments to the first script.</p>

<p>So, overall, this script is used to launch a Rofi dmenu for user input, and then open a new Kitty terminal window to display the results of the cheat script with the user's input as arguments.</p>
<div class="paragraph-with-image">
  <p>And this is how it looks the request <code>python read csv</code>.</p>
  <img src="/img/posts/2023-04-23-rofi-cheat-sheets/2023-04-23_06-30.png">
</div>

<p>In conclusion, the cheat script and the Rofi menu provide a simple and convenient way to search for programming-related information on cheat.sh, with the option to customize the appearance of the menu and the terminal window. The cheat script can be easily integrated into your workflow and adapted to your needs. Additionally, you can map a keybind to quickly access the cheat Rofi menu according to your window manager or desktop environment, making it even more efficient to use.</p>

<p>The scripts used in this tutorial can be found in the <a href="https://github.com/sebastiandevops/.awesomedotfiles">dotfiles</a> repository on GitHub, along with other configuration files for various tools and applications.</p>
