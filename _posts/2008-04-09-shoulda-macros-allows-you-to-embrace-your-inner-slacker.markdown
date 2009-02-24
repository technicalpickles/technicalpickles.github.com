--- 
permalink: /posts/shoulda-macros-allows-you-to-embrace-your-inner-slacker.html
title: Shoulda macros allows you to embrace your inner slacker
tags: 
- rails
- ruby
- shoulda
- testing
layout: post
---
So I think I've discovered [Shoulda](http://thoughtbot.com/projects/shoulda)'s secret sauce, the reason why you should use it.

__Macros.__

It's nice and all to have a decent syntax for doing nested contexts, and declaring tests. But the real power comes from having macros that define tests for you.

Here's a fully shoulda-ified model test (from an example on the Shoulda website):

<pre><code class="ruby">class PostTest < Test::Unit::TestCase
  load_all_fixtures

  should_belong_to :user
  should_have_many :tags, :through => :taggings

  should_require_unique_attributes :title
  should_require_attributes :body, :message => /wtf/
  should_require_attributes :title
  should_only_allow_numeric_values_for :user_id
end</code></pre>

Imagine how much more code you'd have if you were to do this by hand.

It's not magic though. It's pretty easy to write your own. To start with, you can just define them in your `test_helper.rb`.

Here's the jist of what you do:

 * Define a class method, usually like `should_something_something`, or maybe `something_should_something`.
 * In this method, declare a `should` block. You'd probably want to name this dynamically based on the parameters given to your method.
 * Assert stuff.
 * ...
 * PROFIT!

As an example, here's something I wrote recently:

<pre><code class="ruby">class Test::Unit::TestCase
  #snip
  def self.should_build_request_path(path)
    should "build request path of #{path}" do
      assert {path == @api_call.build_request_path}
    end
  end
  # snip
end</code></pre>

Then in your tests, you can use it like:

<pre><code class="ruby">class GetTagsCallTest < Test::Unit::TestCase
  # snip
  should_build_request_path '/v1/tags/get'
  # snip
end</code></pre>

It's pretty straightforward stuff.

If you start to get a lot of these in your `test_helper.rb`, you can always move them into module, include/extend them. If they are general enough, who knows, maybe release it as a gem.