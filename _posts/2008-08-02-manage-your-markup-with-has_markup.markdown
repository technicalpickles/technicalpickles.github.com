--- 
permalink: /posts/manage-your-markup-with-has_markup.html
title: Manage your markup with has_markup
tags: 
- markdown
- plugin
- rails
- ruby
layout: post
---
I recently decided to extract some plugins, in an effort to clean up the codebase for my blog.

The first thing I tackled was generation of HTML from my `Post` model.

### In the beginning...

Here's the starting point for my model:

<pre><code class="ruby">class Post < ActiveRecord::Base
  # --- SNIP ---

  # TODO move into own file... and plugin?
  def self.validates_markdown(*attrs)
    validates_each(*attrs) do |record, attr, value|
      begin
        BlueCloth.new(value).to_html unless value.nil?
      rescue BlueCloth::FormatError => e
        errors.add attr, "has #{e}"
      end
    end
  end
  validates_presence_of :content
  validates_markdown :content
  before_save :cache_content

  # Use BlueCloth to generate HTML ahead of time.
  def cache_content
    html = BlueCloth.new(self.content).to_html
    self.cached_content = html
  end

  # --- SNIP ---
end</code></pre>

 * `validates_markdown` really doesn't belong here
 * Nothing particularly interesting is happening, but the lines of code start to add up
    
Let's look at the partial for `Post`.

<pre><code class="haml">%div{ :id => dom_id(post), :class => dom_class(post) }
  %h2.title= link_to(post.title, post)
  .content~ post.cached_content</code></pre>
  
 * Eh, nothing particuarly interesting
 
### Goals

 * One-liner for specifying markup, similar to `has_many` or `acts_as_taggable`
 * Optionally require the markup
 * Optionally cache the markup
 * Allow for different syntaxes

### A whole new look on things

I was able to extract the logic out of my model and hit all of these goals.

The refactored model:

<pre><code class="ruby">class Post < ActiveRecord::Base
  # --- SNIP ---

  has_markup :content, :required => true, :cache_html => true

  # --- SNIP ---
end</pre></code>
    
And the view is more or less the same:

    %div{ :id => dom_id(post), :class => dom_class(post) }
      %h2.title= link_to(post.title, post)
      .content~ post.cached_content_html

### Thoughts

 * Plugins are really easy to make
 * Plugins can pull a good amount of code out of your model, which results in it being more readable
 * I think I finally understand the difference between include and extend

### Get it
 
If you're interested in trying this plugin, it is [hosted on GitHub](https://github.com/technicalpickles/has_markup/tree). You can install it by with:

    script/plugin install git://github.com/technicalpickles/has_markup.git 