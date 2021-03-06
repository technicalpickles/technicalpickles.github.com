--- 
title: Shoulda and assert{2.0} make swell bedfellows
tags: 
- assert2.0
- rspec
- ruby
- shoulda
layout: post
---
I have been generally using [RSpec](http://rspec.info/) for my testing needs, but I've recently been persuaded to use [Shoulda](http://thoughtbot.com/projects/shoulda) on a few projects.

For most things, they seem about on par.

One thing I missed in particular was the 'should' syntax of RSpec, like:

{% highlight ruby %}
x.should == 42
{% endhighlight %}

As opposed to using test-unit's:

{% highlight ruby %}
assertEqual 42, x
{% endhighlight %}

I haven't been able to entirely quantify why I prefer the former syntax, but I think it has to do with more succinctly expressing the assertion.

While I haven't found quite to replace that style syntax, I do have something that fits my craving: [assert {2.0}](http://www.oreillynet.com/ruby/blog/2008/02/assert2.html). This gives us something like:

{% highlight ruby %}
assert {x == 42}
{% endhighlight %}

I think I'm actually digging this over the other two styles, because:

 * If you can come up with an expression, you can assert it.
 * Nice output when the assertion fails.
