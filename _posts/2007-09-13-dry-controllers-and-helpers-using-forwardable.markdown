--- 
permalink: /posts/dry-controllers-and-helpers-using-forwardable.html
title: DRY Controllers and Helpers using Forwardable
tags: 
- authentication
- bdd
- dry
- forwardable
- rails
layout: post
---
I don't know about you, but I've found myself wanting the same methods available in both a controller and a view. In my case, I had an 'authorized?' method for determining if a user is logged in.

Initially, I had two implementations, one in ApplicationController, and a different one in ApplicationHelper. I was young, and naive.

After starting some [BDD](http://errtheblog.com/post/4268) with [test/spec](http://chneukirchen.org/blog/archive/2007/01/announcing-test-spec-0-3-a-bdd-interface-for-test-unit.html) and [mocha](http://errtheblog.com/post/40), I ran into problems _because_ they were different. It was past time to be more DRY.

So let's think about this. As it turns out, 'controller' is a method available to your views, so, if you have 'something' in your controller, you should be able to hit it in your view by going 'controller.something'. This would give you something like:

    <% if controller.authorized? %>
      Aieeeeeeeeee
    <% end %>

I don't really like how that reads though. If only I could get it to read like I originally had when there were two implementations

    <% if authorized? %>
      Aieeeeeeeeee
    <% end %>

I then remembered reading long ago that you Ruby has built in constructs for doing delegation. But alas, that was ages ago and my memory was blank. After racking my mind, jumping up and down, and praying the seach engine deities, I found it: [Forwardable](http://www.ruby-doc.org/core/classes/Forwardable.html)

My intent is to delegate authorized? to the controller. Here's what the ApplicationHelper ends up looking like:

    module ApplicationHelper
      extend Forwardable
      def_delegator :controller, :authorized?
	  
	  ...
	end

Simple and awesome. I can dig that.
