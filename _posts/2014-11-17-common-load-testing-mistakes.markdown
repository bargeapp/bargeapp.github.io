---
layout: post
title:  Common Load Testing Mistakes
date:   2014-11-17
tags: load-testing 
summary: >
  Where we've gone wrong as an industry and common load testing mistakes.
author: noah
---

### When's the last time you ran a load test?

Most companies I speak to have either never load tested or don't load test on a regular basis. That's unfortunate because a small development team can churn out thousands of lines of code a week - that's a lot of potential bugs! There are many reasons why software might seem to work fine but fail at load; caching, network, database, and SQL related issues are common to discover only under load (ORM generated SQL seems to be especially influenced by the butterfly effect). So passing unit tests are _no indication_ that your application will continue to function under significant load which is precisely when the most is at stake!

### What are you (really) testing?

The most common load testing mistake I see is load testing the infrastructure instead of the application. Sending a large number of HTTP requests to your server gives meaningless data for most applications. A user making that same request is going to do so in a browser which will download and then process the page; resources will download, Javascript will The user's actions on the page will probably cause some Javascript code to fire off a number of additional asynchronous requests. So at best you'll be testing a fraction of the application and at worst you'll be testing your load balancer instead of the application!

The other big load testing anti-pattern I see is the _missing the forest for the trees_ test. This type of test tries to circumvent the limitations of testing outside the browser by capturing the requests made _inside a browser_ and then repeating those at scale. The problem with this approach is its inflexibility; while code is changing daily the test remains static. The only time this test is really useful is the moment after it's created - after that it's become stale and may not be following the same paths as a real user.

### Keep your chin up

If you've made these mistakes the good news is that you're not to blame - the tooling is at fault. While it's super simple to create unit tests in just about any language and modern CI tools take minutes to set up, load testing tools are actually a pain to configure and use. Our industry has done a great job emphasizing and tooling unit and functional testing but somehow load testing seems to have been largely forgotten. Ironically, as internet access has become more ubiquitous and web applications more complex, load testing has never been more important.