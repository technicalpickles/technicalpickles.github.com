--- 
title: "Rails special sauce: Test::Unit setup and teardown"
tags: 
- activesupport
- rails
- ruby
- testing
layout: default
---
Rails adds plenty of special sauce to Ruby standard library.

Being a fan of testing and all that, let me show you what I found while spelunking around the Rails supporting test cases:

### The revelation

<pre><code class="ruby">module ActionMailer
  class TestCase < ActiveSupport::TestCase
    setup :initialize_test_deliveries
    protected
      def initialize_test_deliveries
        # ... snip ...
      end
    # ... snip ...
  end
end</code></pre>

### The shock

What the?!?! I thought you had to `def setup` for setup methods.... but here, it's a method call, that takes a symbol.

### The realization

This reminds me of something...

<pre><code class="ruby">class Subscription < ActiveRecord::Base
  before_create :record_signup

  private
    def record_signup
      self.signed_up_on = Date.today
    end
end</code></pre>

Shocking to say, but they are similar, because they both use [ActiveSupport::Callbacks](http://api.rubyonrails.org/classes/ActiveSupport/Callbacks.html). Basically, this allows us to specify setup and teardown blocks the same way we've been specifying [ActionController filters](http://api.rubyonrails.org/classes/ActionController/Filters/ClassMethods.html) and [ActiveRecord callbacks](http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html):

 * Using a method name as a symbol
 * Using a block
 * Using a method
 * Using conditions
  * Determined by a lambda
  * Determined by a method name as a symbol

### The experiment

Let's try using these all together:

<pre><code class="ruby">require 'rubygems'
require 'active_support'
require 'active_support/test_case'
require 'test/unit'

class ExampleTest < Test::Unit::TestCase
  def setup
    puts "called from def setup"
  end
  
  setup :setup_with_symbol
  setup :setup_never_called, :if => :never
  setup :setup_never_called, :if => lambda { false }

  setup do
    puts "called from a block"
  end
  
  def setup_with_symbol
    puts "called by using symbol of a method name"
  end
  
  def test_nothing
    puts "I am testing nothing"
    assert true
  end
  
  def setup_never_called
    flunk "Should never be displayed"
  end
  
  def never
    false
  end
end</code></pre>

This outputs:

    Loaded suite test-test
    Started
    called by using symbol of a method name
    called from a setup block
    called from def setup
    I am testing nothing
    .
    Finished in 0.000371 seconds.

    1 tests, 1 assertions, 0 failures, 0 errors

### The conclusion

So why is this significant?

 * Isolate related setup into their own methods
 * Have `setup` and `teardown` be conditionally done
 * Allows superclasses to give default `setup` and `teardown`, while allowing subclasses to have their own, without needing to call `super`