--- 
title: Making factory_girl even easier on Rails
tags: 
- factories
- factory_girl
- plugin
- rails
- ruby
- testing
layout: default
---
[factory_girl](http://thoughtbot.com/projects/factory_girl) is pretty sweet. I'm not going over to rehash its awesomeness, but you can check out [its website](http://thoughtbot.com/projects/factory_girl).

Not that it was difficult to use with Rails, but now it's even easier.

I present to you the-oh-so-creatively-named [factory\_girl\_on\_rails](http://github.com/technicalpickles/factory_girl_on_rails/tree/master).

### Install it

    script/plugin install git://github.com/technicalpickles/factory_girl_on_rails.git

### What it does

This plugin allows you to place factory definitions in `test/factories`, and the plugin will automatically load them.

Additionally, it provides a simple generator for creating new factories:

    ./script/generate factory account

This creates an Account factory: test/factories/account_factory.rb

    ./script/generate factory post title:string body:text published:boolean

This creates a Post factory with a string title, text body, and published flag.

### The source

It lives on GitHub: [technicalpickles/factory_girl_on_rails](http://github.com/technicalpickles/factory_girl_on_rails/tree/master)

### Summary

Overall, this plugin doesn't do a whole lot, but I had to do this kind of thing by hand on enough projects that I figured it was worth it.