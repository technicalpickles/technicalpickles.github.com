--- 
permalink: /posts/follow-up-haml-no-longer-be-blowing-my-spot.html
title: "Follow up: Haml no longer be blowing my spot"
tags: 
- haml
- rails
- ruby
layout: post
---
I just barely posted about [Haml blowin my spot](/posts/haml-be-blowing-my-spot). Thanks to the kindness of a reader, [Haml](http://haml.hamptoncatlin.com/) is no longer blowin my spot.

### The problem

Imagine we have the following snippet:

{% highlight haml %}
%div{ :id => "post_#{ post.id }" }
  %h2{:class => 'post_title'}= post.title
  .post_content= post.cached_content
{% endhighlight %}
  
This `post.cached_content` contains some `<pre>` tags. As a result, the contents get kind of skewed.

### The fix

So, it seems you can do something like:

{% highlight haml %}
%div{ :id => "post_#{ post.id }" }
  %h2{:class => 'post_title'}= post.title
  .post_content~ post.cached_content
{% endhighlight %}


The key here is that we use `~` instead of `=` to output stuff. Apparently this is an alias for [Haml::Helpers.find\_and\_preserve](http://haml.hamptoncatlin.com/docs/rdoc/classes/Haml/Helpers.html#M000004). I actually didn't see any documentation about this alias though.

### Looking to the future

[Nathan Weizenbaum](http://nex-3.com/) also pointed out that "Incidentally, as of Haml 1.9 pre and textarea will automatically eschew indentation-management." Sick!
