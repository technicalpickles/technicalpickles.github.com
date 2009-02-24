--- 
permalink: /posts/refactoring-a-activerecord-callback.html
title: Refactoring an ActiveRecord callback
tags: 
- activerecord
- rails
- refactoring
- ruby
layout: post
---
Inspired by a few articles and pesentations ([1](http://giantrobots.thoughtbot.com/2008/1/2/designing-without-ifs) [2](http://giantrobots.thoughtbot.com/2007/5/1/coding-without-ifs) [3](http://giantrobots.thoughtbot.com/2008/2/15/when-rails-plugin) [4](http://giantrobots.thoughtbot.com/2008/6/2/slides-from-railsconf)), I decided it was time to cleanup some of the logic in my `Post` model related to a particular [ActiveRecord callback](http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html). The fact that I needed some comments to explain what it is doing should be a red flag.

<pre><code class="ruby">before_validation :update_published_at_if_necessary

def published?
  self.is_published == true
end

def unpublished?
  ! published?
end

protected
  # Ensure that published_at is set accordingly.
  # 
  #  * Unpublished posts should not have this set
  #  * Published posts should have it set to the current time
  def update_published_at_if_necessary
    if new_record? && published? && published_at.nil?
      self.published_at = Time.now
    end
    if ! published? && !published_at.nil?
      self.published_at = nil
    end
  end</code></pre>
  
Fortunately, I have tests in place that excercise this logic, so as long as my tests are passing, the refactorings must work (hopefully!).
  
My first impression is that the callback is doing too much work. Let's split it up into two pieces.

<pre><code class="ruby">before_validation :set_published_at_to_now_if_necessary
before_validation :unset_published_at_if_necessary

protected
  def set_published_at_to_now_if_necessary
    if new_record? && published? && published_at.nil?
      self.published_at = Time.now
    end
  end
  
  def unset_published_at_if_necessary
    if ! published? && !published_at.nil?
      self.published_at = nil
    end
  end</code></pre>
  
That's somewhat better. At least the methods are more focused.

Hmm, I probably don't need to check the existing `published_at` value when something isn't published. I can also make use of `unpublished?`

<pre><code class="ruby">def unset_published_at_if_necessary
  if unpublished?
    self.published_at = nil
  end
end</code></pre>

I would do something similar for `set_published_at_if_necessary`, but I don't want to override the `published_at` if it was explicitly set. Maybe I post something in the future, or past. I go a little crazy sometimes with that.

I could probably simplify the conditional logic in `set_published_at_if_necessary` by making a new method.

<pre><code class="ruby">protected
  def set_published_at_to_now_if_necessary
    if new_published_post_without_published_at?
      self.published_at = Time.now
    end
  end
  
  def new_published_post_without_published_at?
    new_record? && published? && published_at.nil?
  end</code></pre>

Having `if_necessary` into the method names are kind of bugging me. `before_filter` supports `:if` and `:unless` options, so we should use those, and remove the conditionals from the callback methods.

<pre><code class="ruby">before_validation :set_published_at_to_now, :if => :new_published_post_without_published_at?
before_validation :unset_published_at, :if => :unpublished?

protected
  def set_published_at_to_now
    self.published_at = Time.now
  end
  
  def unset_published_at
    self.published_at = nil
  end</code></pre>

Looking good. Looking pretty, pretty good.

Let's see the finished product:

<pre><code class="ruby">before_validation :set_published_at_to_now, :if => :new_published_post_without_published_at?
before_validation :unset_published_at, :if => :unpublished?

def published?
  self.is_published == true
end

def unpublished?
  ! published?
end

protected
  def set_published_at_to_now
    self.published_at = Time.now
  end
  
  def unset_published_at
    self.published_at = nil
  end
  
  def new_published_post_without_published_at?
    new_record? && published? && published_at.nil?
  end</code></pre>

I'm pretty happy with this. Reads really well. Some of the methods have kind of long names, but I can deal with that.

The only part I don't really like is `published?` and `unpublished?`, but that's for another day.