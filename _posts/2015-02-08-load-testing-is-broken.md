---
layout: post
title:  "Load Testing is Broken"
date:   2015-02-08
tags: selenium, testing
author: noah
---

We write integration tests to test that our web application works properly but we run those tests in a vacuum - on a CI server with a single user. Then we release that code into the wild to be used by a lot of humans and are surprised when things break!

Yes, yes, there's load testing. But why are we still testing our websites load capacity as if they're [GeoCities](http://en.wikipedia.org/wiki/GeoCities) pages? Modern websites aren't contained in a single HTTP response (with the exception of [Berkshire Hathaway](http://www.berkshirehathaway.com/)) - they make use of a lot of javascript and backend service to create the interface. Tools like *ab* and *JMeter* and services built on them are useful for testing the *throughput* of your servers or load balancers (e.g. before deciding on a provider) but they're just not capable of testing the availability and functionality of modern web applications! To really test your web app you need to execute javascript and call remote services in the same way the end user does. In short, you need a <strike>web browser</strike> **lot** of web browsers.

There are some tools on the market that promise to test your site with a whole lot of real web browsers but they come at a high maintenance cost. You're still going to need your integration tests but now you need to maintain a whole new set of tests built using an entirely different toolset. That's a lot of work especially when you don't run those tests very often. So more often than not the tests become stale; new features are built that aren't load tested and existing pages change slighly, breaking the load tests. And the next time you run your load tests you either spend way too much time reworking them or you throw caution to the wind and skip testing a good chunk of features because you just don't have the time to (re)write the tests.

Speaking of testing frequency, why don't we load test more often? Modern tooling makes it super simple to run integration tests on every check-in. Thousands of lines of code are changing *every day* in your codebase, shouldn't you be load testing regularly too? Don't you want to spot load regressions as soon as they're introduced (and certainly before they hit production)?

Barge was built to modernize testing. Barge runs your *existing* Selenium tests at scale and works with any language and framework. Barge uses real browsers so you're testing your *application* and not your load balancer. And Barge is built for integration - a simple REST API makes it a breeze to update your existing tests to use Barge and configure them to run in any CI server.

Here's the obligatory animated gif showing how simple it is to update an existing test, in this case a Capybara test (the full code used in that gif is available at [in this gist](https://gist.github.com/hackerhasid/1cb5949d1055a1fed3db)):

![This is not a kitten, sorry](/assets/update_capybara_for_barge.gif)