---
layout: post
title:  "Integrating Barge: Capybara"
date:   2015-02-10
tags: capybara, selenium, integration
author: noah
---

# Remote Selenium Driver

Capybara provides a Selenium driver out of the box. But this doesn't support remote selenium servers (like Barge). Luckily there's a ruby gem called `capybara-selenium-remote` that plugs directly into Capybara to add this functionality. To install, just add it to your Gemfile:

{% highlight ruby %}
# Gemfile
gem 'capybara-selenium-remote', group: :test
{% endhighlight %}

(you'll need to run `bundle install` but can wait until the next step)

# Install the Barge SDK

Barge provides a ruby SDK via a gem named 'barge-sdk'. Add that to your Gemfile:

{% highlight ruby %}
# Gemfile
gem 'barge-sdk'
{% endhighlight %}

and run `bundle install`

# Configuring Barge

To initialize the Barge SDK you'll need your API key which is available in the [My Account](https://www.bargeapp.com/account) page.

Unlike other tests, running Barge tests on every commit is overkill. Ideally you should be running Barge tests nightly (or at the very least before every release). If you use a CI server one simple way to tell your tests to run a Barge test instead of your regular integration tests is by setting an environment variable. That's the method I'll be here.

### Steps

There are 3 basic steps to modifying your existing scripts:

1. Configure the Barge client
2. Request a new webdriver session & configure the Selenium driver to use it
3. After running your existing test, request a new Barge test

### Code

I'll just post the code and then go through it (note: this code uses Minitest but it's fairly simple to modify for RSpec or other testing frameworks):

{% highlight ruby %}
def setup
  # existing setup
  if ENV['barge']
    # in case you're not doing this above
    Capybara.default_driver = :selenium

    # no need to run the Rails server as Barge will hit the publically available staging server
    Capybara.run_server = false

    # you should probably store your api key in your secrets.yml file or similar
    @barge_client = Barge::Client.new({api_key: 'abcdef123456'})

    configure_selenium_for_barge
  end
end

def teardown
  if ENV['barge']
    # this will queue up a new Barge test after each test runs
    @barge_client.create_webdriver_test({
      webdriver_session_id: @barge_session['id'],
      users: 500,
      minutes: 10
    })
  end
end

private
def configure_selenium_for_barge
  # start a new webdriver session on barge
  @barge_session = @barge_client.create_webdriver_session

  # wait for it to be ready
  while session['status'] == 'pending'
    sleep 2
    @barge_session = @barge_client.describe_webdriver_sessions(session['id'])
  end

  # configure selenium remote driver
  Capybara::Selenium::Remote.use(@barge_session['ip'], {
    url: "http://#{@barge_session['ip']}:#{@barge_session['port']}/"
  })
end
{% endhighlight %}


1. In the `setup` method we check for the presence of a 'barge' environment variable. Its existance will signify the desire to run a Barge script and the code will continue to configure the Barge client and call the `configure_selenium_for_barge` method.

2. `configure_selenium_for_barge` requests a new webdriver session and waits for the session to be ready for testing. When completed it configures the Selenium Remote driver to use the generated ip & port

3. After each existing test (not shown in this code snippet) runs, the `teardown` method will be invoked. Again it will check for the existance of a 'barge' environment variable and if that exists it will ask Barge to run a test

Barge tests run asynchronously. That is, the call to `@barge_client.create_webdriver_test` tells Barge to run a test but the test does not immediately and the SDK does not block the 10 minutes it would take for the test to run! You will receive an email after the test runs alerting you to any issues encountered. You can also use the SDK to poll for test results but I'll leave that for another post.