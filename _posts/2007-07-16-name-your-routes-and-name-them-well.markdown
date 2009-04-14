--- 
title: Name your routes, and name them well
tags: 
- named routes
- rails
layout: post
---
In my [last installment](/blog/permalink/adding-a-tag-listing.html), I added a tag listing. For this listing, we links that look like:

    <%= link_to tag.name, {:controller => 'blog', :action => 'by_tag',
      :tag => tag.name} %>

With a little work, we can refactor this to:

    <%= link_to tag.name, posts_by_tag_url(:tag => tag.name) %>

`posts_by_tag_url`!?! You may be boggled by this. It's pretty easy actually. When you're defining your routes, you can choose to give it a name, instead of the normal 'anonymous' routes.

Here's an anonymous route:

    map.connect 'blog/by_tag/:tag' :controller => 'blog',
      :action => 'by_tag'

Here's the equivalent named route:

    map.posts_by_tag 'blog/by_tag/:tag' :controller => 'blog',
      :action => 'by_tag'

The advantage of using named routes is that it provides you some abstraction over the specific controller/action being used in the link. For example, before I refactored `tags` to `by_tag`, I may have had a link that looked like:

    <%= link_to tag_name, :controller => 'blog', :action => 'tags',
      :tag => tag_name %>

As soon as I changed this to `by_tag`, I needed to fix this, and everywhere it occurs. If I had been using the named route, it wouldn't have been an issued.
