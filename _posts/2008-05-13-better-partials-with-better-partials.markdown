--- 
permalink: /posts/better-partials-with-better-partials.html
title: Better partials with better-partials
tags: 
- better-partials
- haml
- rails
layout: post
---
The other week, [Dan Croak](http://dancroak.com) pointed out [better-partials](http://www.railsjedi.com/posts/22-Better-Partials-Plugin-for-Ruby-on-Rails) to me. Hadn't gotten a chance to play it until now.

The gist of better-partials is that it's a syntactical sugar for `render :partial`.

Here's a typical haml snippet using a partial:

{% highlight haml %}
- form_for(@video) do |form|
  = render :partial => '/videos/form', :locals => { :form => form }
{% endhighlight %}

With better-partials, we can refactor it to:

{% highlight haml %}
- form_for(@video) do |form|
  = partial 'videos/form', :form => form
{% endhighlight %}

This saves a little typing, and makes it a bit more clearer:

 * You just say you want a partial, rather than to render a partial
 * Automatically passes in options to `:locals` (with a few special exceptions)
 
There are a few other usages of better-partials, but I haven't gotten to use them yet.

It's not that big of a deal, but it seems like a nice little improvement to me.
