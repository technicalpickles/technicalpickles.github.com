--- 
permalink: /posts/the-big-rewrite-is-here
title: The Big Rewrite is Here
tags: 
- acts_as_taggable_on_steroids
- hpricot
- piston
- rails
- rspec
- ultraviolet
- vlad
layout: post
---
When I first started this blog, it was my very first Rails project. I was young. I was naive. I didn't do automated testing.

Fast forward back to September, and I wanted to start adding some new features. I wanted to do it right though, with tests, and all. To my dismay, I found the way I had implemented things weren't so easy to test.

This, along with Rails 2.0 coming along, and some other new libraries/plugins I wanted to learn, led me to starting a complete rewrite.

So, here's a laundry list of what I've been using so far:

 * [Rails edge](http://wiki.rubyonrails.org/rails/pages/EdgeRails)
 * [RSpec and RSpec on Rails edge](http://rspec.rubyforge.org/) for BDD goodness
 * [acts\_as\_taggable\_on\_steroids](http://agilewebdevelopment.com/plugins/acts_as_taggable_on_steroids) for tagging
 * [exception\_notification](http://agilewebdevelopment.com/plugins/exception_notifier)
 * [google\_analytics](http://www.rubaidh.com/projects/google-analytics-plugin/)
 * [permalink\_fu](http://www.seoonrails.com/even-better-looking-urls-with-permalink_fu) cuz everyone likes permalinks
 * [scope\_out](http://code.google.com/p/scope-out-rails/)
 * [will\_paginate](http://errtheblog.com/posts/47-i-will-paginate) for sexy pagination
 * [Vlad the Deployer](http://rubyhitsquad.com/Vlad_the_Deployer.html) for deployment
 * [Piston](http://piston.rubyforge.org/) for managing edge and plugins
 * [ultraviolet](http://ultraviolet.rubyforge.org/), [radiograph](http://agilewebdevelopment.com/plugins/radiograph), and [Hpricot + Ultraviolet](http://termos.vemod.net/hpricot-plus-ultraviolet) for syntax highlighting

I think I'm mostly feature complete, but the most significant missing piece is...

Comments! I mostly need to find a good way of spam filtering. The original version was using [simple_captcha](http://agilewebdevelopment.com/plugins/simple_captcha), but I've been noticing spam has still been getting through. I'm considering trying [akismet](http://akismet.com/), which does have a few Rails plugins.

But yeah, that's it for now. I hope to pump out a few posts about this new stuff I'm using.