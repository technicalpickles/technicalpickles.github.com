---
title: Capybara (and Cucumber) and Domains
layout: post
tags:
- rails
- capybara
- domains
- cucumber
- subdomain-fu
---

While hacking on a new application, it took a good few weeks to find out how to, using [cucumber](http://cukes.info/) and [Capybara](https://github.com/jnicklas/capybara), interact with domains that weren't localhost. Consider this a note to my future self :)

Let's imagine your Rails application has a parimary domain and allows users to have subdomains (as if, granted by the power of [subdomain-fu](https://github.com/mbleigh/subdomain-fu)). By default, everything is hitting localhost, for example:

{% highlight ruby %}
visit "/about" # => http://localhost/about
{% endhighlight %}

To get around this, you can tell Capybara to use a different domain:

{% highlight ruby %}
Capybara.default_host = "awesome.com"
visit "/about" # => http://awesome.com/about
{% endhighlight %}

I found it useful to make a step to navigate to a particular domain:

{% highlight ruby %}
When /^I visit (.*)$/ do |site_domain|
  site_domain = "localhost" if site_domain == "the main domain"

  Capybara.default_host = site_domain
  visit "/"
end
{% endhighlight %}

Armed with this, we can write our features:

{% highlight cucumber %}
Scenario: Visit the main domain
  When I visit the main domain
  Then I should see information about the main domain

Scenario: Visit another domain
  When I visit awesome.com
  Then I should see awesomeness
{% endhighlight %}
