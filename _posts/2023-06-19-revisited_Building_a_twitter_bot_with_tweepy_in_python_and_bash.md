---
layout: post
title: "Revisited: Building a Twitter Bot with Tweepy in Python and Bash"
subtitle: "How to use Tweepy to create a twitter bot using Python and Bash."
date: 2023-06-19 05:00:13 -0400
background: '/img/posts/2023-05-28-my-twitter-bot/2023-05-28_tweepy_background.png'
---
<p><b>Introduction:</b></p>
<p>Welcome to the revised edition of our article on building a Twitter bot using Tweepy in Python and Bash! In this updated version, we will explore the advancements and enhancements that have been made since the original release of our bot.</p>

<p>Version 2 of our Twitter bot brings significant improvements, including the migration to the Twitter API v2.0 and the adoption of object-oriented programming (OOP) principles. These changes have allowed us to leverage the latest features offered by the Twitter API and implement a more modular and maintainable code structure.</p>

<p>With the release of Twitter API v2.0, we gain access to a range of powerful capabilities, such as improved filtering options, enhanced tweet search functionality, and more robust rate limits. We'll dive into the specifics of these new features and demonstrate how they can be utilized to enhance the functionality and performance of our bot.</p>

<p>In addition to the API migration, we have refactored our codebase to embrace object-oriented programming. By organizing our code into classes and objects, we achieve greater encapsulation, reusability, and maintainability. This shift towards OOP allows us to create a more modular and extensible bot, making it easier to add new features or modify existing ones without introducing unnecessary complexity.</p>

<p>In this revised article, we will delve into the enhanced version of our Twitter bot built with Tweepy and explore the advancements introduced in version 2. While this article focuses on the new concepts and improvements, it is recommended that readers familiarize themselves with the basics covered in the initial version of the article. If you haven't already, we encourage you to check out our previous article, <a href="https://sebastiandevops.github.io/2023/05/28/my-twitter-boy-in-python-and-bash.html">'My Twitter bot in Python and Bash: How to use Tweepy to create a Twitter bot using Python and Bash'</a>, which provides a comprehensive explanation of the foundational aspects of building a Twitter bot with Tweepy. In this revisited version we'll cover topics such as authentication, tweet generation, posting tweets, handling errors, and more, all while emphasizing the benefits of adopting a modular and object-oriented approach.</p>

<p>So, whether you're new to building Twitter bots or seeking to upgrade your existing bot to take advantage of the latest API version and OOP principles, this article is designed to provide you with comprehensive guidance and insights. Let's dive in and explore the exciting world of building a powerful and intelligent Twitter bot with Tweepy in Python and Bash!</p>

<p>The purpose of the bot is to gather fascinating facts from a website that publishes daily historical events and occurrences. It accomplishes this by employing a web scraper that extracts data from the website's HTML files. The extracted information is then stored in a file for further processing. Using the Tweepy library, the bot accesses the stored data and crafts intriguing tweets. These tweets are then published on a regular basis, sharing intriguing and curious facts with the bot's followers.</p>

<p><b>Architecture</b></p>
<p>The TweepyBot consists of three main modules: config.py, models.py, and services.py.</p>

<p><b>config.py</b></p>
<p>This module handles the authentication to the Twitter API. It uses the tweepy.Client class from the Tweepy library to create an instance of the authenticated Twitter API. The required authentication credentials (consumer key, consumer secret, access token, and access token secret) are obtained from environment variables. If the authentication is successful, the API instance is returned.</p>

{% highlight python3 %}
#!/usr/bin/python3

# tweepy-bots/bots/config.py
import tweepy
import os


def create_api():
    """
    Authenticates to the Twitter API using the provided environment variables.

    Returns:
        tweepy.Client: An instance of the authenticated Twitter API.

    Raises:
        Exception: If an unexpected error occurs during authentication.

    Note:
        Before calling this function, ensure that the following environment
        variables are set:
        - TWITTER_CONSUMER_KEY: The Twitter API consumer key.
        - TWITTER_CONSUMER_SECRET: The Twitter API consumer secret.
        - TWITTER_ACCESS_TOKEN: The Twitter API access token.
        - TWITTER_ACCESS_TOKEN_SECRET: The Twitter API access token secret.
    """
    consumer_key = os.getenv("TWITTER_CONSUMER_KEY")
    consumer_secret = os.getenv("TWITTER_CONSUMER_SECRET")
    access_token = os.getenv("TWITTER_ACCESS_TOKEN")
    access_token_secret = os.getenv("TWITTER_ACCESS_TOKEN_SECRET")

    try:
        api = tweepy.Client(
                  consumer_key=consumer_key,
                  consumer_secret=consumer_secret,
                  access_token=access_token,
                  access_token_secret=access_token_secret
              )
        print("Successfully authenticated!")
        return api
    except Exception as e:
        print(f'Error during authentication: {e}')
{% endhighlight %}


<p><b>models.py</b></p>

<p>This module contains the TweepyBot class, which represents the Twitter bot powered by Tweepy. It has various attributes and methods to handle the tweet generation and posting.</p>

<p><b>Attributes:</b>
    <ul>
        <li><code>api:</code> The instance of the authenticated Twitter API obtained from the config.py module.</li>
        <li><code>hashtag:</code> The hashtag to be included in the tweet.</li>
        <li><code>date_format:</code> The format for the date to be included in the tweet.</li>
        <li><code>data:</code> The data to populate the tweet.</li>
        <li><code>text:</code> The custom text to be included in the tweet, overriding other tweet components if provided.</li>
        <li><code>source:</code> The data source for the tweet.</li>
        <li><code>cleaner:</code> A boolean indicating whether to execute a data cleaner.</li>
    </ul>
</p>

<p><b>Methods</b>
    <ul>
        <li><code>prepare_tweet():</code> Retrieves the line to be posted on Twitter by combining the tweet components based on the provided attributes.</li>
        <li><code>post_tweet(text):</code> Publishes a tweet or thread using the Twitter API.</li>
        <li><code>__str__():</code> Returns a string representation of the TweepyBot object.</li>
    </ul>
</p>

{% highlight python3 %}
#!/usr/bin/env python3

from app.services import get_line, create_tweet
from app.config import create_api


class TweepyBot:
    """
    A class representing a Twitter bot powered by Tweepy.

    Attributes:
        api: The Twitter API object used for interacting
             with the Twitter platform.
        hashtag: The hashtag to be included in the tweet.
        date_format: The formatted date to be included in the tweet.
        data: The data to populate the tweet.
        text: The custom text to be included in the tweet.
              Overrides other tweet components if provided.
        source: The data source for the tweet.
        cleaner: A boolean indicating whether to execute a data cleaner.
                 False by default.

    Methods:
        get_tweet():
            Retrieves the line to post on Twitter, combining the tweet
            components based on the provided attributes.
            Returns:
                A string representing the tweet content.

        post_tweet(mystr):
            Publishes a tweet or thread using the Twitter API.
            Args:
                mystr: The string to be published as a tweet.

    Usage:
        # Create an instance of TweepyBot
        bot = TweepyBot(api=create_api(), hashtag="🤖", date_format=None,
                        data=None, text=None, source=None, cleaner=False)

        # Retrieve the tweet content
        tweet_content = bot.get_tweet()

        # Post the tweet
        bot.post_tweet(tweet_content)
    """
    def __init__(
        self,
        api=create_api(),
        hashtag="🤖",
        date_format=None,
        data=None,
        text=None,
        source=None,
        cleaner=False
    ):
        self.api = api
        self.hashtag = hashtag
        self.date_format = date_format
        self.text = text
        self.data = data
        self.source = source
        self.cleaner = cleaner

    def prepare_tweet(self):
        """
        Retrieves the line to post on Twitter, combining the tweet components
        based on the provided attributes.

        Returns:
            A string representing the tweet content.
        """
        try:
            if self.text is not None:
                text = f'{self.hashtag} {self.text}'
            elif self.data is None and self.text is None:
                text = "Default tweet."
            else:
                text = get_line(
                    self.hashtag,
                    self.date_format,
                    self.data,
                    self.source,
                    self.cleaner
                )
            return text
        except Exception as e:
            # Handle the exception (e.g., log the error or display a message)
            print(f"An error occurred while generating the tweet: {str(e)}")
            return None

    def post_tweet(self, text):
        """
        Publishes a tweet or thread using the Twitter API.

        Args:
            mystr: The string to be published as a tweet.
        """
        try:
            create_tweet(self.api, text)
        except Exception as e:
            # Handle the exception (e.g., log the error or display a message)
            print(f"An error occurred while posting the tweet: {str(e)}")

    def __str__(self):
        """repr method.

        Args:
            None

        """
        representation = "[TweepyBot]\
                          \nAPI: {}\
                          \nHashtag: {}\
                          \nDate format: {}\
                          \nData: {}\
                          \nSource: {}\
                          \nCleaner: {}".format(self.api,
                                                self.hashtag,
                                                self.date_format,
                                                self.data,
                                                self.source,
                                                self.cleaner)
        return representation


if __name__ == "__main__":
    pass
{% endhighlight %}

<p><b>services.py</b></p>
<p>This module contains helper functions used by the TweepyBot class to generate and post tweets.</p>

<p><b>Functions</b>
    <ul>
        <li><code>get_line(hashtag, date_format, data, source, cleaner):</code> Retrieves a line to be tweeted based on the provided parameters. It reads the data file, optionally applies a cleaner, and formats the line.</li>
        <li><code>read_file(data, cleaner):</code> Reads the file and optionally cleans the line if the cleaner flag is set.</li>
        <li><code>create_tweet(api, text):</code> Creates a single tweet or thread using the Twitter API. It handles splitting longer tweets into multiple tweets if necessary.</li>
        <li><code>split_string(string):</code> Splits a string into segments based on Twitter's character limit.</li>
        <li><code>get_date(date_format):</code> Creates a formatted date based on the provided date format.</li>
    </ul>
</p>

<p><b>Functionalities</b></p>
<p><b>The TweepyBot provides the following functionalities:</b>
    <ul>
        <li>Authentication to the Twitter API using environment variables.</li>
        <li>Generation of tweets based on the specified components (hashtag, date, data, text, source) and formatting.</li>
        <li>Posting of tweets using the Twitter API, handling both single tweets and threaded tweets for longer content.</li>
        <li>Support for cleaning the source data file by removing already used lines.</li>
        <li>Formatting of dates in English or Spanish based on the specified date format.</li>
    </ul>
</p>

<p>To use the <code>TweepyBot</code>, you can create an instance of the <code>TweepyBot</code> class with the required parameters and then call the <code>get_tweet()</code> method to retrieve the tweet content. Finally, you can call the <code>post_tweet()</code> method to publish the tweet.</p>

Example usage:
{% highlight python3 %}
# Create an instance of TweepyBot
bot = TweepyBot(api=create_api(), hashtag="🤖", date_format=None, data=None, text=None, source=None, cleaner=False)

# Retrieve the tweet content
tweet_content = bot.prepare_tweet()

# Post the tweet
bot.post_tweet(tweet_content)
{% endhighlight %}

<p>In this revised version of our article, we explored the evolution of our Twitter bot built with Tweepy, as we transitioned from version 1 to version 2. We embraced the power of the Twitter API version 2.0 and leveraged the capabilities it offered to enhance the functionality and efficiency of our bot. Through modularization and the adoption of object-oriented programming principles, we achieved a more organized and maintainable codebase.</p>

<p>By upgrading to version 2, we were able to take advantage of new features and improvements, such as the enhanced authentication process, advanced tweet creation options, and more efficient data retrieval from the Twitter platform. These enhancements not only improved the performance of our bot but also opened up possibilities for further innovation and expansion.</p>

<p>We hope this revised article has provided you with valuable insights into the advancements introduced in Tweepy version 2 and inspired you to explore the potential of building your own Twitter bots. Whether you're a beginner or an experienced developer, Tweepy remains a powerful tool for creating intelligent, automated interactions on the Twitter platform.</p>

<p>Remember to check out the first version of our article, which covers the fundamentals of building a Twitter bot using Tweepy, for a comprehensive understanding of the basics. As you continue your journey, keep exploring the capabilities of Tweepy and discovering new ways to make your bots more engaging, creative, and impactful.</p>

<p>Happy bot-building!</p>
