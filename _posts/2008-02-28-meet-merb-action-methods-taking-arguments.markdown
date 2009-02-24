--- 
permalink: /posts/meet-merb-action-methods-taking-arguments.html
title: "Meet Merb: Action methods taking arguments"
tags: 
- merb
- ruby
layout: post
---
Have you ever written a controller action like:

<pre><code class="ruby">class PostsController < ApplicationController
  def create
    @post = Post.new(params[:post])
    @post.save
  end
end
</code></pre>

Notice how we yank something out of the request parameters? Wouldn't it be nice if you could just define that as an argument to the action method?

Well, merb-action-args does exactly that.

<pre><code class="ruby">class Posts < Application
  def create(post)
    @post = Post.new(post)
    @post.save
  end
end
</code></pre>

Superficially, for this example, there isn't really much of a difference. But, I do feel like it shows your intent more clearly. It basically says, "The create method takes a post."

How does it work internally? It seems to be using [ParseTree](http://parsetree.rubyforge.org/) voodoo.

When an action is about to be called, the method arguments are looked up. Then, for each argument is used as a key of the params hash in turn. Finally, the action is called with the appropriate arguments.

If you want to actually use it, you need to install it and add a bit to config/init.rb:

<pre><code>gem install -y merb-action-args
</code></pre>

Note: this is for merb-0.9.0, which is a 'developer-only' release so it's not available from rubyforge yet. See [Michael Ivey's post](http://gweezlebur.com/2008/2/14/merb-0-9-0-released-kinda) about how to install.

<pre><code class="ruby">dependency "merb-action-args"
</code></pre>

If you want to hack on merb-action-args, it's  part of [merb-more](http://github.com/wycats/merb-more/tree/master), which is hosted on [GitHub](http://github.com)