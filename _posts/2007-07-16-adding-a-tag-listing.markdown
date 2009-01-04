--- 
title: Adding a tag listing
tags: 
- acts_as_taggable_on_steroids
- rails
- tags
layout: default
---
My next move was to be able to have a page listing all the known tags in the system. I first refactored the `tags` action to `by_tag`, so that it reads a bit better, and so I could use `tags` action for my tag list action.

Here's what the new `tags` action looks like:

    def tags
      @tags = BlogPost.tag_counts
    end

This `tags_counts` is actually added to your model when you add `acts_as_taggable` to your model class. The name is a bit misleading, but it does provide you a list of tags for the given model. The `Tag` object also includes a count of how many things are using it, hence the name `tag_counts`. The acts_as_taggable homepage suggests this was intended to help you generate a tag cloud.

Here's what my rhtml looks like:

    <h2>All known tags:</h2>
    <ul>
      <% @tags.each do |tag| %>
        <li><%= link_to tag.name, {:controller => 'blog', :action => 'by_tag', :tag => tag.name} %></li>
      <% end %>
    </ul>

And with that, I have a some nice tag listing action going on. In a future iteration, I hope to turn this into a tag cloud.