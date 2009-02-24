--- 
permalink: /posts/helper-testing-using-actionview-testcase.html
title: helper testing using ActionView::TestCase
tags: 
- helper
- rails
- ruby
- shoulda
- testing
layout: post
---
Testing testing testing. We test our models, our controllers, and the integration between them. But how often do we pay attention to helpers? Not very often, if the test coverage of my current project's helpers is any indicator.

There are a few tools out there for testing your controllers. There's a plugin called [helper_test](http://nubyonrails.com/articles/test-your-helpers). [ZenTest](zentest.rubyforge.org/) also provides [Test::Rails::HelperTestCase](http://zentest.rubyforge.org/ZenTest/classes/Test/Rails/HelperTestCase.html).

There's an even better way, and it's included with Rails itself since at least 2.1: [ActionView::TestCase](http://api.rubyonrails.org/classes/ActionView/TestCase.html). The name might be misleading, but it's intended for testing view helpers. It also is a kind of underdocumented.

You can [skip ahead to the summary](#helper_testing_summary) if you want the dirty details.

### Your very first helper test

The Rails test layout doesn't have an obvious place to put helper tests. I'll take a cue from helper_test, and say to put them in `test/unit/helpers`. That way, they will be run by `rake test:units`.

### The skeleton test

Let's start out with testing `ApplicationController`, in `test/unit/helper/application_helper_test.rb`:

<pre><code class="ruby">require File.dirname(__FILE__) + '/../../test_helper'
require 'action_view/test_case'
class ApplicationHelperTest < ActionView::TestCase
  def test_nothing
    assert true
  end
end</code></pre>

Some notes:

 * We do a typical require of `test/test_helper.rb`
 * I'm requiring 'action\_view/test\_case', because without it, we see an error like `uninitialized constant ActionView::TestCase (NameError)`. I feel like ActiveSupport should find this automatically, but it isn't. You can also toss this in `test/test_helper.rb`.
 * Inherit `ActionView::TestCase`
 * Placeholder test
 
### What to test

I want to add a helper for displaying a navigation tab. It will just go to index of a controller you specify. The link should have the 'current' class if you're rendering from controller you are making a tab for.

Based on that, we have two contexts to test:

 * Making a tab for the current controller
 * Making a tab for another controller

### Test it first

You'd always be using the helper from a specific controller, so we'll have a parent context of being in a controller, like `EventsController`.

Now that we have some context, and some specifications, let's translate this into a [Shoulda](http://thoughtbot.com/projects/shoulda)'s terminology.

<pre><code class="ruby">context &quot;creating a tab for 'events'&quot; do
  setup do
    @tab = tab_for('events')
  end

  should &quot;list item with a link to events with 'current' class&quot; do
    assert_dom_equal '&lt;li&gt;&lt;a href=&quot;/events&quot; class=&quot;current&quot;&gt;Events&lt;/a&gt;&lt;/li&gt;', @tab
  end
end

context &quot;creating a tab for 'projects'&quot; do
  setup do
    @tab = tab_for('projects')
  end

  should &quot;list item with a link to projects without 'current' class&quot; do
    assert_dom_equal '&lt;li&gt;&lt;a href=&quot;/projects&quot;&gt;Projects&lt;/a&gt;&lt;/li&gt;', @tab
  end
end</code></pre>

The parent context doesn't do anything yet, because we don't know any implementation details about how it's going to determine the current controller. `assert_dom_equal`, as the name implies, checks that the DOM of the given strings are equivalent. Presumably, this takes care of whitespace, casing, etc.

Let's run it the first time... RED, because we don't have any implementation.

### First pass at implementing it

Let's try a first implementation:

<pre><code class="ruby">module ApplicationHelper
  def tab_for(name)
    url = send(&quot;#{name.downcase}_path&quot;)
    content_tag :li do
      link_to(name.capitalize, url)
    end
  end
end</code></pre>
    
Briefly put:

 * We're generate a url. This presumes that the there's a path defined like `events_path`, as if by `map.resource :events`.
 * Make a `<li>`
  * Make a link to the URL we just generated

Let's see how this fares... some GREEN, but still RED. Creating a tab for another controller passes, but creating a tab for the current controller fails. That's because we never determine if we're on on the current controller. How are we going to do that? Here's some fun facts towards enlightenment:

 * Helpers are `include`d in the view.
 * The view exposes `controller`, which is the rendering controller
 * ActionController::Base exposes `controller_name`, which has a stringified name of the controller, ie 'events' for `EventsController`.
 
I want to check the value of `controller.controller_name`. We can stub `controller` to return an actual `EventsController` using [mocha](http://mocha.rubyforge.org/):

<pre><code class="ruby">context "When on the events controller" do
  setup do
    self.stubs(:controller).returns(EventsController.new)
  end
  # ...
end</code></pre>


And... we have the same failures. No wonder, because we didn't change the implementation. Now to give it another try:

<pre><code class="ruby">def tab_for(name)
  url = send("#{name.downcase}_path")
  content_tag :li do
    attributes = {}
    attributes[:class] = 'current' if controller.controller_name == name
    link_to(name.capitalize, url, attributes)
  end
end</code></pre>
    
Wait for it, wait for it... GREEN!

<h3 id="helper_testing_summary">Summary</h3>

I believe that wraps things up. Let's just do a quick recap:

 * Stash tests in `test/unit/helpers`
 * Name your tests like your helper, ie `ApplicationHelperTest` for `ApplicationHelper`
 * Inherit from `ActionView::TestCase`
 * `require 'action_view/test_helper'` somewhere, probably in `test/test_helper.rb`
 * Your helper will be `include`d into your test, so you can just use its methods
 * If you need to touch stuff that would normally be included in the view, stub it out
 * Use `assert_dom_equals` to test the output. Alternative, you might use `assert_equal` or `assert_match`
 
### Gotchas

Testing helpers this way is not quite bullet proof though. While coding this up, I encountered a few tough errors that spit out a stack trace because of a `nil` value somewhere down in the Rails stack. For, I tried using `url_for` instead of using the named routes, and `ActionView::TestCase` didn't take too kindly to that.