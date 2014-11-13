---
layout: post
title:  Introducing Barge
date:   2014-11-11
tags: barge load-testing phantomjs casperjs
summary: >
  Today we're launching Barge - the only load testing platform that uses your existing functional test scripts as load tests. Barge monitors the state of your application and alerts stakeholders of performance issues before they’re pushed to production. 
author: noah
---
## TLDR;
Today I'm very happy to announce the launch of <a href="https://www.bargeapp.com">bargeapp.com</a>. Barge is built for developers and is the only load testing platform that uses your existing functional test scripts as load tests. We've launched with support for PhantomJS and CasperJS scripts and plan on adding more frameworks soon.

I invite you to check it out!

## The Backstory

I joined GroupCommerce (acquired by NimbleCommerce in 2013) as the first engineering employee after the CTO. The company showed a lot of promise from the beginning - the executive team was comprised of ex-DoubleClick/Googlers and had existing relationships with the publishers we were targeting as customers. It was a very exciting time!

As a B2B company working with big-brand publishers (DailyCandy, Thrillist, New York Times) we had some unique load patterns. We doubled our user base overnight on more than one occasion just by signing a new customer. Of course we investigated different load testing tools to help us ensure we could handle the traffic. So I got experience using various load testing tools and didn't love any of them. So I set out to create a load testing tool that developers could actually love.


## Enter Barge

Barge integrates with GitHub and taps into your existing development practices and runs your _existing_ functional tests as load tests. Load testing is traditionally an afterthought but Barge aims to bring load testing up to par with unit and functional testing by supporting test schedules and CI integrations. And because Barge uses real web browsers it's capable of testing complex workflows and single-page applications.

Out of the gate we support PhantomJS and CasperJS scripts and we plan to add support for more functional test frameworks soon. Getting started is easy, just:

1. Visit [www.bargeapp.com](https://www.bargeapp.com)
2. Login with Github
3. Enable a repository
4. Add a comment containing `@barge` to the top of the scripts you'd like to import
5. Push your code
6. Start testing!

Here's a sample PhantomJS script to get you started:

{% highlight javascript %}
// @barge suite:"Homepage"

var url = "" // ENTER YOUR DOMAIN NAME HERE
var page = require('webpage').create();

// triggers callback after DOM is loaded - see https://gist.github.com/firedfox/2475509
page.onInitialized = function() {
  page.evaluate(function(domContentLoadedMsg) {
    document.addEventListener('DOMContentLoaded', function() {
      window.callPhantom('DOMContentLoaded');
    }, false);
  });
};
page.onCallback = function(data) {
  console.log('DOMContentLoaded');
  phantom.exit(0);
};

page.open(url);
{% endhighlight %}

Happy testing!

\- Noah Blumenthal,
Founder @ BargeApp.com
