---
layout: post
title: Webdrivers - Introduction to Selenium
categories: Others

---

## What are Webdrivers?

Ever seen a person do too well on an online game? Or maybe spam you messages at an inhuman speed? Well this is what Webdrivers do. They weren't designed to do this of course. Webdrivers are easy to use APIs for Web Application Testing, they allow us to control the behaviour of our browser towards sites using the provided API. People do use them for Testing Servers but there are a lot of fun uses too.

## Get Started with Selenium

[Selenium](http://www.seleniumhq.org/projects/webdriver/) is the Michael Jordan of webdrivers. The ultimate, most complete webdriver you can find. Thinking it's pretty hard to use? The [documentation](http://selenium-python.readthedocs.io/) is pretty neat but let me give you a demonstration anyway. To setup the python bindings, simply open your terminal and do:

{% highlight shell %}
pip install selenium
{% endhighlight %}


Now, selenium comes with built in drivers for a variety of browsers, including Chrome, Firefox, Safari, Opera etc. I prefer using the Firefox driver since that is my default browser but the code should be relatively similar for all sorts of browsers. Let's start up a simple instance of our favorite browser:

{% highlight python%}
from selenium import webdriver
driver = webdriver.Firefox()
{% endhighlight %}


As soon as you press Enter after that last line, an instance of Firefox should fire up. Now you have complete control over it from your Python interpreter. You can do all sorts of stuff with it. 


## Going a Little Further

I'll help you get started by demonstrating how you can write a script to log you into your facebook account whenever your PC starts up without you pressing even one key. So first thing's first. You should have imported selenium and started an instance of your favorite browser. Next let's navigate to the facebook login page with:

{% highlight python %}
driver.get('https://www.facebook.com/login')
{% endhighlight %}


Great, so far so easy. Now what a human does next is fill in his E-Mail and Password in the respective fields. We'll make Selenium do the same. But it needs to know where to put the E-Mail and the Password. So we first make it look for the fields using:

{% highlight python %}
email_box = driver.find_element_by_id('email')
password_box = driver.find_element_by_id('pass')
{% endhighlight %}

What we do here is simply make the driver look for an element with the id *email* and *pass* respectively which is generally the ID for such fields. If you are trying this on another site and it fails, simply go to the site and navigate to the respective field, then right click and select Inspect Element, and you can figure out the ID. If that particular element does not have an ID attribute, Selenium provides you with other options for searching like using the name,the xpath, the tag name etc. Hopefully, in this case though Selenium found the respective fields. Next we type in our data in the fields:

{% highlight python %}
from selenium.webdriver.common_keys import Keys
email_box.send_keys("mycrappymail@somedomain.com")
pass_box.send_keys("sweetpotatoesareyummy")
{% endhighlight %}

Hopefully, these lines were pretty self-explanatory. Now, just one last thing we need to do. Press the login button. We follow the same steps, look for the button first, then click it.

{% highlight python %}
login_button  = driver.find_element_by_id('loginbutton')
login_button.click()
{% endhighlight %}


There it is. Your Facebook page logged in. Now all you have to do is. Copy all these commands to a python script, add it to your startup applications. And you have your Facebook page open at Computer startup without pressing a button.

## Doing more with the same

Now this was possibly the simplest thing you could do with the Selenium Webdriver. You could automate filling forms so you never have to enter your details anywhere ever again. You could make a customized crawler for looking through pages for something you want. And of course, game bots or spam bots are probably the must fun thing to do. I might go on to make some of these if I write more about Selenium