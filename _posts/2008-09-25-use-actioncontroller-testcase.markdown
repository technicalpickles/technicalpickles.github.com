--- 
title: Use ActionController::TestCase
tags: 
- rails
- ruby
- testing
layout: default
---
I've been coming across a lot of Rails functional tests that look like this: 

<pre><code class="ruby">require File.dirname(__FILE__) + '/../test_helper'
require 'owners_controller'

# Raise errors beyond the default web-based presentation
class OwnersController; def rescue_action(e) raise e end; end

class OwnersControllerTest < Test::Unit::TestCase
  def setup
    @controller = OwnersController.new
    @request = ActionController::TestRequest.new
    @response = ActionController::TestResponse.new
    # ...
  end
  # ...
end</code></pre>

Some of it is repetitive (you always have to always create the controller, request, and response, and reopening the class). Some is unnecessary (requiring the file).

I'm not sure when it was added, but Rails provides ActionController::TestCase which does this boilerplate stuff up for you. Here's what we can change to it to:

<pre><code class="ruby">require File.dirname(__FILE__) + '/../test_helper'

class OwnersControllerTest < ActionController::TestCase
  def setup
    # ...
  end
  # ...
end</code></pre>

Much cleaner, right?