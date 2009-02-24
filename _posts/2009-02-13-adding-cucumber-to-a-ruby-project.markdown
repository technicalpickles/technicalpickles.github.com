--- 
permalink: /posts/adding-cucumber-to-a-ruby-project.html
title: Adding Cucumber to a Ruby project
tags: 
- cucumber
- minitest
- rspec
- ruby
- testing
- testunit
layout: post
---
I had [announced recently](http://technicalpickles.com/posts/jeweler-now-with-support-for-test-unit-minitest-rspec-and-cucumber) that [jeweler](github.com/technicalpickles/jeweler/) now supports generating the files needed to use cucumber in your project. Personally, I've always been annoyed when looking for documentation about, for example, adding cucumber to your project, and then come across stuff that is like "Oh hai, just generate a project with jeweler, lol."

I don't want to be that guy, so here's the process I used for adding Cucumber to the testing mix of jeweler.

### Background

I'm not going to go any details about actually using Cucumber, so you'll want to do a little reading ahead of time. I'd recommend the [Cucumber website](http://cukes.info/), the [Cucumber Wiki on GitHub](http://wiki.github.com/aslakhellesoy/cucumber), and [The RSpec Book (currently in beta)](http://www.pragprog.com/titles/achbd/the-rspec-book).

### File layout

 * Everything cucumber should live in `features` directory.
 * [Features](http://wiki.github.com/aslakhellesoy/cucumber/feature-introduction) describing your project live in this `features` directory
 * `features/support/env.rb` sets up the [world](http://wiki.github.com/aslakhellesoy/cucumber/a-whole-new-world) that the features will be run under
 * [Steps](http://wiki.github.com/aslakhellesoy/cucumber/step-definitions) live in the `features/step_definitions` directory
 
Here's some convient bash one-liner for making the directories:

    mkdir -p features/{support,step_definitions}

### env.rb

You do have one choice to make here... what framework do you want to use implement your steps? The wiki has instructions for using [Test::Unit](http://wiki.github.com/aslakhellesoy/cucumber/using-testunit), [MiniTest](http://wiki.github.com/aslakhellesoy/cucumber/using-minitest), and [RSpec](http://wiki.github.com/aslakhellesoy/cucumber/rspec-expectations).

It's also worth noting that `env.rb` is the cucumber-equivalent of `test_helper.rb` or `spec_helper.rb`, so do any configuration or `require`ing here. For example you probably want to require your main ruby file from the `lib` directory. For jeweler, I did:

<pre><code class="ruby">$LOAD_PATH.unshift(File.dirname(__FILE__) + '/../../lib')
require 'jeweler'</code></pre>

### Rake configuration

You do have a `Rakefile`, right? Given the file layout above, you can add this snippet:

<pre><code class="ruby">begin
  require 'cucumber/rake/task'
  Cucumber::Rake::Task.new(:features)
rescue LoadError
  puts &quot;Cucumber is not available. In order to run features, you must: sudo gem install cucumber&quot;
end</code></pre>

### Summary

Adding cucumber to a project is pretty straightforward, but all the info was never in one place. Hopefully, this article addresses that. Now you can have fun writing your features and step definitions.