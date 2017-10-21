---
layout: post
title: Dataset Shortages Pt. 1 - Introduction to Scrapy
categories: Data-Science
---

## Dealing with lack of Data

Deep Learning and Machine Learning are fun hobbies. Endless projects to do. Lots of Open Source Code available. Nothing to stop you. Except Datasets. If you are up to generic problems, then you will probably come across a dataset if you look hard enough. But if you are trying to solve a weird problem. Looking for a dataset can be nightmarish. Let's see if there are ways you could build your own dataset overnight.

Data Scraping is basically looking for sites with the right data. Taking their HTML and structuring it to a form which is suitable to train your Neural Net, etc. How is this done you ask? The answer to the question is same as the answer to most of our problems. Look for a library/framework in Python!

This particular framework is called [**Scrapy**](https://scrapy.org/). And it is nothing short of art. Scrapy gives you the left hand of Picasso so you can call yourself a great artist. You can send requests and extract fields in lesser lines than it takes to write "Hello World" in Java. How to get started? As with any other Python Framework, the [documentation](https://docs.scrapy.org/en/latest/) is golden. Have a look at it and you will be ready to go in under an hour. Let me take you through some of it. 


## Getting Used to Scrapy

First of all get it installed with:


{% highlight shell %}
pip install scrapy
{% endhighlight %}

After the setup is complete. You can go two ways. You can either start a project straightaway or first get warmed up using the Scrapy shell. The Scrapy shell is a command shell for interactive scraping. You can test your code using the shell before moving to a proper project. Let's try getting the list of all flowers on this [site](http://www.all-my-favourite-flower-names.com/) from the shell.

Open the shell with:

{% highlight shell %}
scrapy shell http://www.all-my-favourite-flower-names.com/list-of-flower-names.html
{% endhighlight %}

The HTML Response is saved in the 'response' variable. You can use CSS or Xpath selectors to get data from the HTML. For example, let's get the title of the page using both Xpath and CSS Selectors:

{% highlight python %}
response.xpath('//title/text()').extract()  ##Using Xpath Selectors
response.css('title::text').extract()  ##Using CSS Selectors
{% endhighlight %}

The title of the page should be returned. Simple enough, right? 


## On to more complex tasks

Let's get all the flower names on this particular page. As you may have noticed. The name of the flowers are inside a *b* tag inside a *span* tag inside a *p* tag. So to get the names out:

{% highlight python %}
response.xpath('//p/span/b/text()').extract()  ##Using Xpath Selectors
response.css('p span b::text').extract()  ##Using CSS Selectors
{% endhighlight %}

Surprisingly simple. If your page is slightly more complicated. Try to get closer to the data you need using the nearest *div* tag in the DOM Tree. Identify the *div* tag using it's class or ID or any other such attribute and then move further inside till you have the data you need.

Once you know your selectors are spot on, you can begin working the logic for navigating to all the pages your data is on. For this particular example, we have more pages with flower names alphabetically arranged. How do you move across all those pages? You need to get all those links first. The links can be easily taken out with:

{% highlight python %}
response.css('p b a::attr(href)').extract()
{% endhighlight %}


Now you have a list of links. You can simply cycle through all these links and run your selector each time to get all the names. Another way is to move page by page instead of getting all the links at the beginning. For that, once you have gotten data from the first page, you look for the next link to go to. This approach is more common when all the links may not be available on the first page itself. For our example the first approach works just fine.

## What's Next?

There's a lot more to it. **Scrapy** provides middlewares, allows you to set custom headers, let's you create your own item pipelines and write custom link extractors. I'll dive into this later. This part should be enough to explore simple sites. Build your own dataset for your Deep Learning or Machine Learning Problems or just gather data because you love data. It's fun anyways. You can go all detective trying to figure out which selectors and tags will be just right for you to pick up the data you need in the most efficient manner.