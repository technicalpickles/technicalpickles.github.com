--- 
permalink: /posts/that-s-a-lot-of-tags.html
title: That's a lot of tags!!
tags: 
- acts_as_taggable_on_steroids
- rails
- tags
layout: post
---
With the help of [acts_as_taggable_on_steroids](http://agilewebdevelopment.com/plugins/acts_as_taggable_on_steroids) I now have taggable blog posts!

It was really dead simple, just followed the instructions on their page.

What good is collecting tagging information if you don't display it, so of course, I want to add it to the partial used for the blog posts. I thought to myself, well, this is a list, so wouldn't it make sense to be a `<ul>`? Sure, but definitely not your typical `<ul>` with the bullets and all. I remember reading about using CSS trickery to make `<ul>`s look like comma separated list, and dug up something over [here](http://milov.nl/2883).

Here's the important snippet:

    ul.links { list-style: none; margin: 0; padding: 0; }
    ul.links li { display: inline; }
    ul.links li:after { content: ","; } 
    ul.links li:last-child:after { content: ""; }

I wanted it to be a bit more generic, so I called the class 'comma-separated'. I thought a little more about it, and realized I was doing pipe-separated lists without using `<ul>`. After some tweaking, I ended up with this CSS:

    ul.comma-separated, ul.pipe-separated { list-style: none; margin: 0; padding: 0; }
    ul.comma-separated li, ul.pipe-separated li { display: inline; }
    ul.comma-separated li:last-child:after, ul.pipe-separated li:last-child:after { content: ""; }

    ul.comma-separated li:after { content: ","; } 
    ul.pipe-separated li:after { content: " | "; } 

Here's the final code snippet I added to my `_blog_post.rhtml` partial:

    <div class="post_tags">
      <ul class="comma-separated">
        <% blog_post.tag_list.names.each do |tag_name| %>
          <li><%= link_to tag_name, :action => 'tags', :id => tag_name %></li>
        <% end %>
      </ul>
    </div>

As the code suggests, I also added a new action, for displaying all items matching a particular tag. It looks a little bit like:

    def tags
      # FIXME do routing magic to change :id to :tag
      @blog_posts = BlogPost.find_tagged_with params[:id]
      render :action => 'all'
    end

As the fixme points out, I really would like to be using `:tag`, since it's kinda abusing the meaning of `:id`. I tried hooking this up in my `config/routes.rb`, but it didn't quite hold up:

    map.connect 'blog/tags/:tag', :controller => 'blog', :action => 'tags'

In any event, I'd say it works well enough for now, given how little time it took.
