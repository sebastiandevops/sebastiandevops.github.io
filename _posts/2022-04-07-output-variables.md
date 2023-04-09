---
layout: post
title: "Output variables in Azure Devops"
subtitle: "Using output variables during pipeline execution in Azure DevOps Classic Editor."
date: 2023-04-09 00:45:13 -0400
# background: '/img/posts/chilivim.png'
---

<p>Variables are a crucial component of Azure DevOps that provide a great deal of flexibility. You can define your own variables using libraries that you can import into your pipelines, making them available across all pipelines within your organization.</p>

<div class="img-container">
  <img src="/img/posts/2023-04-07-output-variables/libraries.png">
</div>

<p>However, this isn't the only way to use variables in Azure DevOps. You can also define key-value pairs within your pipeline definitions, which will be available in the scope of your pipeline. Additionally, you can set key-value pairs in specific pipeline tasks to use these variables only within that task.</p>

<p>Sometimes, you may need to store a value during pipeline execution and use it in the pipeline's next step. However, if you simply export the variable, you'll find that the value isn't available in the next step, no matter what you did before. This is because output variables exist only within the scope of the task.</p>

<p>To store a value during pipeline execution and make it available in the next step, you can store it in a variable and then use an ad-hoc script to publish it to the output variables. For example, you can store the hostname in a variable called <code>MY_VAR</code> then use the <code>##vso</code> command to publish it. To accomplish this, you can use a Command Line Task to publish the output variable, then use a Bash Script task to retrieve the value from the previous task.</p>

#### Command Line Task:

{% highlight text %}
export MY_VAR=$(hostname)
echo "This is the hostname stored in MY_VAR: $MY_VAR"
echo "##vso[task.setvariable variable=MY_OUTPUT_VAR;isOutput=true;]$MY_VAR"
{% endhighlight %}

<p>It's important to note that you need to define a reference name as a namespace to ensure that you can access the correct variable in the next task.</p>

<div class="img-container">
  <img src="/img/posts/2023-04-07-output-variables/reference_name.png">
</div>

<p>In the Command Line Task, the raw log output will display the following information:</p>

{% highlight text %}
Starting: Command Line Script
==============================================================================
Task         : Command line
Description  : Run a command line script using Bash on Linux and macOS and cmd.exe on Windows
Version      : 2.212.0
Author       : Microsoft Corporation
Help         : https://docs.microsoft.com/azure/devops/pipelines/tasks/utility/command-line
==============================================================================
Generating script.
========================== Starting Command Output ===========================
/usr/bin/bash --noprofile --norc /home/vsts/work/_temp/33323ee0-3fd9-4343-966e-159b19d00c5d.sh
This is the hostname stored in MY_VAR: fv-az48-991
Finishing: Command Line Script
{% endhighlight %}

<p>In the Bash Script Task, where you call the output variable from the previous task, you'll notice that calling MY_OUTPUT_VAR retrieves the same value as MY_VAR, indicating that you've successfully stored MY_VAR as an output variable.</p>

<p>Furthermore, when accessing the variable in the next task, you must use the reference name and then the variable defined in the previous task.</p>

#### Bash Script Task:

{% highlight text %}
echo "This is the hostname stored as output variable in MY_OUTPUT_VAR: $(OUT.MY_OUTPUT_VAR)"
{% endhighlight %}

#### Output:

{% highlight text %}
Starting: Bash Script
==============================================================================
Task         : Bash
Description  : Run a Bash script on macOS, Linux, or Windows
Version      : 3.214.0
Author       : Microsoft Corporation
Help         : https://docs.microsoft.com/azure/devops/pipelines/tasks/utility/bash
==============================================================================
Generating script.
========================== Starting Command Output ===========================
/usr/bin/bash /home/vsts/work/_temp/60b34d90-0d1c-4f71-8aed-d027a0da0da2.sh
This is the hostname stored as output variable in MY_OUTPUT_VAR: fv-az48-991
Finishing: Bash Script
{% endhighlight %}

<p>In conclusion, using variables in Azure DevOps can provide great flexibility and customization for your pipeline. You can define your own variables and make them available across all pipelines within your organization, as well as define key-value pairs within your pipeline definitions or specific pipeline tasks. To store a value during pipeline execution and use it in the pipeline's next step, you can store it in a variable and then use an ad-hoc script to publish it to the output variables. Just remember to define a reference name as a namespace to ensure that you can access the correct variable in the next task.</p>
