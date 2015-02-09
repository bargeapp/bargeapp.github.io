---
layout: post
title:  "Load Testing is Broken"
date:   2015-02-08
tags: selenium, testing
author: noah
---

We write integration tests to test that our web application works properly but we run those tests in a vacuum - on a CI server with a single user. Then we release that code into the wild to be used by a lot of humans and are surprised when things break!

Yes, yes, there's load testing. But why are we still testing our websites load capacity as if they're [GeoCities](http://en.wikipedia.org/wiki/GeoCities) pages? Modern websites aren't contained in a single HTTP response (with the exception of [Berkshire Hathaway](http://www.berkshirehathaway.com/)) - they make use of a lot of javascript and backend service to create the interface. Tools like *ab* and *JMeter* and services built on them are useful for testing the *throughput* of your servers or load balancers (e.g. before deciding on a provider) but they just are not capable of testing the availability and functionality of modern web applications! To really test you need a <strike>web browser</strike> **lot** of web browsers. 

There are some tools that will do that - test your site with a whole lot of real web browsers - but they come at a high maintenance cost. You're still going to need your integration tests but now you need to maintain a whole new set of tests. Which isn't really feasible considering it's also an entirely different toolset. And those load tests are only run once in a while so the tests become stale - new features are built that aren't load tested and existing pages change causing the load tests to break. I guess you'll have to skip the next scheduled load test because there just isn't time to update them all. Speaking of testing frequency, you're probably running integrations tests pretty frequentyly, right? Maybe even on every check-in. So why are you load testing so infrequently? Thousands of lines of code are changing *every day* in your codebase, shouldn't you be load testing nightly to spot load regressions before they hit production?

Barge runs your existing Selenium tests at scale. It works with any language and framework; all you need is an API call before the and after your script. Here's an snippet of a Capybara script updated to use Barge:

{% highlight ruby %}
  # This code assumes the following gems: 'capybara', 'capybara-selenium-remote', barge-sdk'

  require 'capybara/rails'
  require 'barge'

  @client = Barge::Client.new api_key: 'abcdef123456'

  session = @client.create_webdriver_session

  while session['status'] == 'pending'
    sleep 2
    session = @client.describe_webdriver_sessions(session['id'])
  end

  Capybara.default_driver = :selenium
  Capybara::Selenium::Remote.use session['ip'], url: "http://#{session['ip']}:#{session['port']}/"

  # ... do stuff

  @client.create_webdriver_test webdriver_session_id: session['id'], users: 500, minutes: 10
{% endhighlight %}