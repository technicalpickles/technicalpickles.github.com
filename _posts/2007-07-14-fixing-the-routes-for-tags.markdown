--- 
title: Fixing the routes for tags
tags: 
- acts_as_taggable_on_steroids
- rails
- tags
layout: post
---
After my first forray into [tags](/blog/permalink/that-s-a-lot-of-tags.html), I felt like I needed to further refine and enhance my usage of them.

So we had this code chunk:

{% highlight ruby %}
# in app/controllers/blog_controller.rb
def tags
  # FIXME do routing magic to change :id to :tag
  @blog_posts = BlogPost.find_tagged_with params[:id]
  render :action => 'all'
end
    ...
    
# in config/routes.rb
map.connect 'blog/tags/:tag', :controller => 'blog', :action => 'tags'
{% endhighlight %}

The issue was that I wanted to be using `params[:tag]`, not `params[:id]`. The cause was that my `map.connect` was declared AFTER the default connect string, ie:

{% highlight ruby %}
# Install the default route as the lowest priority.
map.connect ':controller/:action/:id.:format'
map.connect ':controller/:action/:id'
{% endhighlight %}

Apparently I didn't read the comments closely enough :) Moving it above the default route, it works like I had hoped.
