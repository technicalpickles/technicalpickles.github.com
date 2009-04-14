--- 
title: Cover your butt with rcov
tags: 
- coverage
- gentoo
- rails
- ruby
- testing
layout: post
---
We all know that test coverage is a good thing, right? Right. In Ruby land, the most common way to do this is to use [rcov](http://eigenclass.org/hiki.rb?rcov). I'll be covering getting rcov installed, and how to start using it with your Rails project.

The first thing to do is install it. On Gentoo, this is as simple as:

    $ sudo emerge rcov

If you aren't fortunate enough to be on Gentoo, or if you just prefer using gems directly, you can just do:

    $ sudo gem install rcov

Now you'll want to install the Rails plugin.

I highly recommend using [piston](http://piston.rubyforge.org) to do this. Among other things, it provides a nice compromise between using svn:externals and just checking in plugins into your own repo. Piston is emergable, or as a gem.

If you go this path, you can do

    $ cd vendor/plugins
	$ piston import http://svn.codahale.com/rails_rcov

Otherwise, use the plugin script with svn:externals...

	$ ./script/plugin install -x http://svn.codahale.com/rails_rcov 

or without svn:externals...

    $ ./script/plugin install http://svn.codahale.com/rails_rcov


We should be in business now.

Try running everything:

    $ rake test:test:rcov

Behold, you now have coverage reports! Check them out at coverage/test/index.html

You should be all good now. Take a chance to look around 'rake -T' to see what other tasks are available for you.

How is my coverage looking? Er, well, turns out I have less than 50% coverage for unit and funcitonal tests, and 0% for integration... looks someone has some tests to write...
