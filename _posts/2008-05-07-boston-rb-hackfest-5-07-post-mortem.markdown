--- 
permalink: /posts/boston-rb-hackfest-5-07-post-mortem
title: Boston.rb Hackfest 5/07 Post Mortem
tags: 
- boston
- hackfest
- rails
- ruby
- testing
layout: post
---
By now, I think it's a running joke that we'd work on the recommendable plugin we started a month or so ago.

Instead, we worked on the Factory stuff. [This article captures](http://www.dcmanges.com/blog/38) most of the ideas of using factories for testing and reasons for using them instead of fixtures. 

The crew tonight:

* [Chad Pytel](http://thoughtbot.com/about/people#cpytel)
* [Josh Nichols](http://technicalpickles.com)
* Wyatt Greene
* Brian Underwood
* [Dan Croak](http://dancroak.com)


## Overview

As the article mentions, [someone did implement a plugin](http://factories-and-workers.googlecode.com/svn/trunk/factories-and-workers/README) which captures most of these ideas.

There were a few reasons (and some more which I don't remember) why we didn't like this plugin.

 * Plugin, not a gem, so unusable outside of rails
 * Pollutes the namespace of `Object`
 * No tests

So, the journey for better Factories begins. The basic ideas/goals we settled on is this:

 * Define your factories in `test/test_helper.rb`
 * Be able to define a factory for a class with default options
 * Be able to define alternate factories for a class, for example, to create an admin user versus a normal user
 * Flexibility for defining default attributes, ie be able to call other methods
 * Choice of making new instance, persisting a new instance, stubbing an instance, or getting valid attributes
 * Flexible API for actually calling the factories
 
## The API
 
So we started out with a basic API that Dan Croak had outlined:

<pre><code class="ruby">Factory.define do |factory|
  factory.add :project do |project|
    project.name {"factoryfu"}
    project.homepage_url {"http://factoryfu.rubyforge.org"}
  end
  
  factory.add :event, :meeting do |event|
    event.event_type {"Meeting"}
    event.date {Time.now}
    event.description {"an sick meeting"}
  end
  
  factory.add :event, :hackfest do |event|
    event.event_type {"Hackfest"}
    event.date {Time.now}
    event.description {"a cool hackfest"}
  end
end</code></pre>
    
As for usage of the factories, we had 3 possibilities:

<pre><code class="ruby">Factory.create :project, :name => 'ambitious_sphinx'
Factory.create_project :name => 'ambitious_sphinx'
create_project :name => 'ambitious_sphinx'</code></pre>

I'm not going to say much about these, aside from the first being the simplest to implement, the second being the most explicit about what you are doing, and the last is the most concise.

Because it seemed like the simplest, we started implementing the first. I believe that the other two syntaxes can actually be emulated using it.

To simulate the second, as we register factories, we can create the appropriate methods, ie `create_event`, `new_event`, `valid_event_attributes`.

To simulate the third, we can build on the second, and probably do some magic to delegate from `TestCase` to our Factory.

## Implementing it

For simplicity's sake, we started with our work from last week's hackfest on [bostonrb.org](http://bostonrb.org).

We added the snippet for defining factories into our `test_helper.rb`, made a new model unit test which used the factory methods, and started a Factory class.

We basically ran the test, watched it blow up horribly, and added some code, until eventually the test passed.

You can see the work we ended up in the [hackfest repository under 'boston\_rb'](https://svn.thoughtbot.com/hackfest/boston_rb)

Of particular interest are:

 * [lib/factory.rb](https://svn.thoughtbot.com/hackfest/boston_rb/lib/factory.rb)
 * [test/test_helper](https://svn.thoughtbot.com/hackfest/boston_rb/test/test_helper.rb)
 * [test/unit/project_test.rb](https://svn.thoughtbot.com/hackfest/boston_rb/test/unit/project_test.rb)

So, I'm just go over a few of the more interesting bits of the implementation:

### Factory.define

This is the entrance into defining our factories.

The implementation isn't very interesting. We just call the block on Factory. We were originally making new instances of Factory, and passing that to the block, but we then realized that this made it a little trickier to have factory methods be class-level methods.

### Factory.add

This is what you use to define how you the factory behaves.

It takes a block which defines how to create new attributes. If you look at the snippet:

<pre><code class="ruby">event.event_type {"Hackfest"}
event.date {Time.now}</code></pre>

Notice that `event.event_type` and `event.date` are actually taking blocks. This allows us to do `Time.now`, and have this not actually be invoked until object creation.

`Factory.add` actually passes your block a magical `Hash` which captures this attribute/attribute creation data. We'll talk about this has in a little bit.

After we get back from the block, we store in `@@model_templates`, keyed by the model and kind of the factory.

### The Factory methods

We provide three factory methods:

 * `valid_attributes`: a Hash of valid attributes for the object
 * `new`: a new unpersisted instance, using `valid_attributes`
 * `create`: a persisted instance, using `new`

You invoke them like:

<pre><code class="ruby">Factory.valid_attributes :project
Factory.new :project, :name => 'factoryfu', :homepage_url => 'http://somewhere.com'
Factory.create :event, :hackfest, :date => 3.days.ago, :description => 'your looking at it'</code></pre>

The way we parse the arguments is a little tricky. Initially, we only supported the first 2 lines. This meant our method signature looked like:

<pre><code class="ruby">def self.new(model, attributes = {})</code></pre>

When we went to support the 3rd line, we thought to go like:

<pre><code class="ruby">def self.new(model, kind = 'default', attributes = {})</code></pre>
    
By doing so, you break the 2nd line, because `kind` would get set to our `attributes`.

activesupport provides a solution: [`Array#extract_options`](http://api.rubyonrails.org/classes/ActiveSupport/CoreExtensions/Array/ExtractOptions.html). Basically, it looks at the `Array`, and pops the last element off the end if it's a `Hash`. We use it like:

<pre><code class="ruby">def self.valid_attributes(model, *args)
  kind, attributes = parse_args(*args)
  # ...
end

protected

def self.parse_args(*args)
  attributes = args.extract_options!
  kind = args.first || 'default'
  [kind, attributes]
end</code></pre>

### BlockfulHash

We made a subclass of `Hash` which lets you define keys like:

<pre><code class="ruby">event.date {Time.now}
event.description {"This is going to be SICK!"}</code></pre>

Such that it's equivalent to:

<pre><code class="ruby">event[:date] = {Time.now}
event[:description] = {"This is going to be SICK!"}</code></pre>

Here's the implementation:

<pre><code class="ruby">class BlockfulHash < Hash
  def method_missing(method, *args)    
    # When Proc.new is given no arguments,
    # it returns a Proc for the block passed to the current method
    # Proc.new... yeah, it's officially a hackfest.
    self[method.to_sym] = Proc.new
  end
end</code></pre>

This `BlockfulHash` is what gets passed to the block given to `Factory.add`.

## What's next

So, we have something workable here:

 * Uses the syntax we laid out for defining factory
 * Supports the first syntax for using factories (`Factory.create :event`)
 
As far as what is left to do:

 * Extract it out of the boston\_rb code base
 * More tests
 * Gemify
 * Support `Factory.create_event` syntax
 * ...
 * PROFIT!!!
 
## Addendum

Some links and other ideas tossed around over the course of the night:

 * [UnitRecord](http://www.dcmanges.com/blog/rails-unit-record-test-without-the-database) for model unit tests without the database
 * [mocha](http://mocha.rubyforge.org) for mocking/stubbing
 * [Object Daddy](http://b.logi.cx/2007/11/26/object-daddy)
 * [Seed Fu](http://www.intridea.com/2008/4/20/seed-fu-simple-seed-data-for-rails)
 * [fixture-scenarios](http://code.google.com/p/fixture-scenarios/)
 * [Rails Scenarios](http://faithfulcode.rubyforge.org/docs/scenarios/)

