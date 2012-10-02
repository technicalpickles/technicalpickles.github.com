--- 
title: Determine if content_for was used
tags: 
- content_for
- html
- rails
- ruby
layout: post
---
One of those common things to do is have a sidebar with content, which is dynamic based on where you are in the app. The common solution uses `content_for :sidebar`, and `yield :sidebar`. In practice, this looks like:

<pre><code class="html.erb">&lt;!-- In app/views/layouts/application.html.erb --&gt;
&lt;html&gt;
  &lt;body&gt;
    &lt;div id="sidebar"&gt;
      &lt;%= yield :sidebar %&gt;
    &lt;/div&gt;
    
    &lt;div id="content"&gt;
      &lt;%= yield %&gt;
    &lt;/div&gt;
  &lt;/body&gt;
&lt;/html&gt;</code></pre>
    
<pre><code class="html.erb">&lt;!-- In app/views/foo/index.html.erb --&gt;
&lt;% content_for :sidebar do %&gt;
  im in ur sidebar showing of ur foo
&lt;% end %&gt;

&lt;ul&gt;
  &lt;li&gt;Foo&lt;/li&gt;
  &lt;li&gt;Bar&lt;/li&gt;
&lt;/ul&gt;</code></pre>

This way, you get the custom content in sidebar when you specify. Here's the kink: you yield the sidebar inside your sidebar div, so the containing sidebar div is always going to be rendered, whether or not there's anything rendered inside of it.

Annoying.

Wouldn't it be awesome to conditionally render the containing div? Yeah, but there's not a builtin way as far as I can tell.

Why don't we take a peek into `content_for`'s implementation, and try to understand what it's doing:

<pre><code class="ruby">def content_for(name, content = nil, &amp;block)
  existing_content_for = instance_variable_get("@content_for_#{name}").to_s
  new_content_for      = existing_content_for + (block_given? ? capture(&amp;block) : content)
  instance_variable_set("@content_for_#{name}", new_content_for)
end</code></pre>

So, if I understand it, for sidebar, it would be doing:

 * Getting existing content in `@content_for_sidebar`
 * Generate content from the block, and combine it with the existing content
 * Set `@content_for_sidebar` to the combined content
 
So, if `@content_for_sidebar` is nil, I think we can safely know that `content_for :sidebar` has not been used. Let's try this out:

<pre><code class="html.erb">&lt;!-- In app/views/layouts/application.html.erb %&gt;
&lt;html&gt;
  &lt;body&gt;
    &lt;% unless @content_for_sidebar.nil? %&gt;
      &lt;div id="sidebar"&gt;
        &lt;%= yield :sidebar %&gt;
      &lt;/div&gt;
    &lt;% end %&gt;
    
    &lt;div id="content"&gt;
      &lt;%= yield %&gt;
    &lt;/div&gt;
  &lt;/body&gt;
&lt;/html&gt;</code></pre>

Does it work? Yes. Am I a naughty, naughty boy who should be punished? That's kind of irrelevant, but it feels kludgy to be checking the Rails internals in the view. How about we do something-a-like-this:


<pre><code class="ruby"># in app/helpers/application_helper.rb
module ApplicationHelper
  def content_given?(name)
    content = instance_variable_get("@content_for_#{name}")
    ! content.nil?
  end
end</code></pre>

<pre><code class="html.erb">&lt;!-- In app/views/layouts/application.html.erb %&gt;
&lt;html&gt;
  &lt;body&gt;
    &lt;% if content_given? :sidebar %&gt;
      &lt;div id="sidebar"&gt;
        &lt;%= yield :sidebar %&gt;
      &lt;/div&gt;
    &lt;% end %&gt;
    
    &lt;div id="content"&gt;
      &lt;%= yield %&gt;
    &lt;/div&gt;
  &lt;/body&gt;
&lt;/html&gt;</code></pre>
    
That definitely feels nicer. Maybe it should be included with Rails? As a plugin? As a gem? All of the above?

