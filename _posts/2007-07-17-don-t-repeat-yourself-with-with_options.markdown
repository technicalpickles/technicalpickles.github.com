--- 
title: Don't Repeat Yourself with with_options
tags: 
- named routes
- rails
- with_options
layout: post
---
After entering the loving embrace of [named routes](/blog/permalink/name-your-routes-and-name-them-well.html), I found myself with the following routes:

    map.posts_by_tag 'blog/by_tag/:tag', :controller => 'blog', :action => 'by_tag'
    map.connect 'blog/by_tag/:tag.:format', :controller => 'blog', :action => 'by_tag'
    map.post_tags 'blog/tags', :controller => 'blog', :action => 'tags'
    map.all_posts 'blog/all', :controller => 'blog', :action => 'all'
    map.permalink 'blog/permalink/:permalink', :controller => 'blog',
      :action => 'permalink'
    map.connect 'blog/permalink/:permalink.:format', :controller => 'blog',
      :action => 'permalink', :format => nil

Yick. Seems like there's a bit of repeated code here. Fortunate for us, Ruby does have mechanisms for dealing with this, `with_options`. Essentially, it does some magic, such that you give it some options, and inside the block you provide it, you're able to indicate you want ot use those options in addition to any other options you may need. That's a bit wordy, so let the code speak for itself:

    map.with_options :controller => 'blog' do |blog|
      blog.with_options :action => 'by_tag' do |blog_by_tag|
        blog_by_tag.posts_by_tag 'blog/by_tag/:tag'
        blog_by_tag.connect 'blog/by_tag/:tag.:format'
      end
      blog.with_options :action => 'permalink' do |blog_permalink|
        blog_permalink.permalink 'blog/permalink/:permalink.:format'
        blog_permalink.connect 'blog/permalink/:permalink'
      end
      blog.post_tags 'blog/tags.:format', :action => 'tags'
      blog.all_posts 'blog/all.:format', :action => 'all'
    end

The resulting code minimizes repetition, and also illustrates the intent much more clearly.
