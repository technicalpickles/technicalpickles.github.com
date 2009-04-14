--- 
title: Implementing a simple logging IRC bot
tags: 
- chat
- irc
- ruby
layout: post
---
For [Boston.rb](http://bostonrb.org), we've been using a public [Campfire](http://www.campfirenow.com/) provided by [thoughtbot](http://thoughtbot.com) as a backchannel during meetings. It's nice and all, except it doesn't provide proper logging. For example:

  * If you come into the channel, there's no way to see what had been previously said.
  * If you lose your connection at some point, like if you close your laptop and take it home, all the discussion you see goes away
  * If you were to try to save the conversation by copy and pasting, you have to massage the copied text a bit to make it less whitespacy

Little known is that there's actually a protocol out there dedicated to chatting across the series of tubes. It's called [internet relay chat](http://en.wikipedia.org/wiki/Internet_Relay_Chat) (aka IRC).

Now, IRC itself doesn't solve these solutions. The advantage is that IRC is an open protocol and has been around a long time. As a result, there are many libraries out there for interacting with it.

I came across one such library, [isaac](http://github.com/ichverstehe/isaac/tree). In a nutshell, it's a DSL akin to [Sinatra](), except it's used for making [IRC bots](http://en.wikipedia.org/wiki/IRC_bot) instead of web applications.

Within 15 minutes of finding this library, I managed to whip up a simple bot which just sits in a channel, and logs it to a file. Check it:

{% highlight ruby %}
require 'rubygems'
require 'isaac'

config do |c|
  c.username = "bostonrbot"
  c.realname = "bostonrb logger bot"
  c.nick    = "bostonrbot"
  c.server  = "irc.freenode.net"
  c.port    = 6667
end

on :connect do
  join "#boston.rb"
end

on :channel, /.*/ do
  open("#{channel}.log", "a") do |log|
    log.puts "#{nick}: #{message}"
  end

  puts "#{channel}: #{nick}: #{message}"
end
{% endhighlight %}

For our user group, I plan on moving our back I plan on just running this bot during our meetings, and then emailing the log out afterwards. I could automate more, but this is a really simple first pass.
