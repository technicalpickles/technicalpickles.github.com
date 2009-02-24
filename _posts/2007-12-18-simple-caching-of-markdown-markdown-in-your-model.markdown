--- 
permalink: /posts/simple-caching-of-markdown-markdown-in-your-model
title: Simple caching of markdown markdown in your model
tags: 
- markdown
- rails
layout: post
---
As I've written about it [before](/posts/using-markdown-in-vim), I'm using [markdown](http://daringfireball.net/projects/markdown/) for [this blog](http://technicalpickles.com). Originally, I would do the translation from markdown to html every time a page is rendered. Seems pretty inefficient, right? Yeah, not so much.

As it turns out, it is really simple to cache this information.

For a little background, here's the schema for my posts:

<pre><code class="ruby">create_table "posts", :force => true do |t|
  t.string   "title"
  t.text     "content"
end</code></pre>

And the model:

<pre><code class="ruby">class Post < ActiveRecord::Base
  validates_presence_of :title
  validates_presence_of :content
end</code></pre>

What I want to do is, before a post is saved, to generate html from the markdown and save it to the database. Then, instead of generating it at page render, use the saved html.

ActiveRecord provides several [callbacks](http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html) into its lifecycle. There are a few ways you can add a callback as the RDoc demonstrates.  The lifecycle we're really interested is before\_save. This is for whenever the post is saved, regardless of it is creating a new one, or updating an existing one.

Before we implement the callback, let's update our schema with a migration.

    $ script/generate migration AddCachedContent
    
<pre><code class="ruby">class AddCachedContent < ActiveRecord::Migration
  def self.up
    add_column :posts, :cached_content, :text
  end

  def self.down
    remove_column :posts, :cached_content
  end
end</code></pre>

Hmm... one problem: we probably want to cache the content of all existing posts. So, let's add a method that will cache the content.

<pre><code class="ruby_on_rails">
class Post < ActiveRecord::Base
  validates_presence_of :title
  validates_presence_of :content
  
  def cache_content
    self.cached_content = BlueCloth.new(self.content).to_html
  end
end
</code></pre>

Now we have the means to cache our markdown, we can update our migration to cache the content of each post and save it:

<pre><code class="ruby">class AddCachedContent < ActiveRecord::Migration
  def self.up
    add_column :posts, :cached_content, :text

    Post.find(:all).each do |post|
      post.cache_content
      post.save!
    end
  end

  def self.down
    remove_column :posts, :cached_content
  end
end</code></pre>

We have everything in place to go ahead and actually add the callback:

<pre><code class="ruby">class Post < ActiveRecord::Base
  validates_presence_of :title
  validates_presence_of :content
  
  before_save :cache_content
  def cache_content
    self.cached_content = BlueCloth.new(self.content).to_html
  end
end</code></pre>

We're just about done. All that remains is to update places in the view that generate html from markdown to just use the cached\_content instead.