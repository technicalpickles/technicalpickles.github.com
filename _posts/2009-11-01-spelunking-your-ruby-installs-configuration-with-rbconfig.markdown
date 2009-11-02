---
layout: post
title: Spelunking your Ruby install's configuration with RbConfig
---
Let me tell you, there is this thing called RbConfig, and it has a treasure trove of information about the configuration of your Ruby installation. Even better is that it comes as part of a stock Ruby installation, so no addition gems are required.

Here's a quick example: I need to know the operating system my ruby code is running, and react accordingly.

{% highlight ruby %}
require 'rbconfig'

# snip my otherwise flawless code
if RbConfig::CONFIG['host_os'] =~ /mswin|windows|cygwin/i
  # insert hacks for windows
end
# snip more otherwise flawless code
{% endhighlight %}

For a complete list of configuration details it contains, I'd suggest busting out an irb, and poking around.

I'd love to go into further detail, but alas, all my past attempts at digging up more information have failed. I only came across it by chance while trying to figure out how to detect if I was on Windows for [jeweler](http://github.com/technicalpickles/jeweler), but even then, it was only code examples that I came across.

What I do know, is that it contains information gathered at the time that you configure and build Ruby.