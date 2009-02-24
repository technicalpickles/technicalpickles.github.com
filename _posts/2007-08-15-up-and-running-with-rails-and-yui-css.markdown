--- 
permalink: /posts/up-and-running-with-rails-and-yui-css.html
title: Up and running with Rails and YUI CSS
tags: 
- css
- rails
- xhtml
- yui
layout: post
---
I've found myself a big fan of the [Yahoo UI](http://developer.yahoo.com/yui/) library recently. I think when most people think Yahoo UI, they imagine the Javascript widgets, like a [degradable Javascript menu](http://developer.yahoo.com/yui/menu/), or [sortable tables](http://developer.yahoo.com/yui/datatable/).

This is only part of it. What I've been more interested in lately are the CSS bits to it. There are four separate CSS 'libraries' they provide. I'm going to briefly describe what each of them do, and then give an overview about how to use them in your Rails project.

### [Reset](http://developer.yahoo.com/yui/reset/)

This library resets all attributes on all elements to a baseline. Font sizes, padding, margin, and so on. I mean everything. All the headings, paragraphs, etc will be the same size. Ordered and unordered lists lose their item decoration. It gives you a blank slate to build on, letting you have full control over your pages.

### [Fonts](http://developer.yahoo.com/yui/fonts/)

This provides you with a way to easily scale your fonts, as well as a means to ensure a sane font is available regardless of the browser that is used.

### [Grids](http://developer.yahoo.com/yui/grids/)

Nearly everyone under the sun does a grid layout at one point or another. This library takes a lot of the work out of it. It provides several different layouts, with varying widths and number of columns.

### [Base](http://developer.yahoo.com/yui/reset/)

This is a recent addition, and part of the reason I've been using these libraries more. It provides a sane defaults for all the HTML elements that got stripped down by reset. This is a pretty nice stop-gap if you don't want to hand craft every single element.

## Getting the goods

Hit up the [download page](http://developer.yahoo.com/yui/download/) to download a zip of the distribution. Version 2.3.0 was the latest at time of writing.

Extract the file somewhere convienent for you to get at, and you'll get a 'yui' directory.

## Getting the files into your rails app

The files we care about are (relative to yui directory):

  * build/reset/reset.css
  * build/fonts/fonts.css
  * build/grids/grids.css
  * build/base/base.css

In your rails app, create the directory 'public/stylesheets/yui'. The specific directory doesn't matter, but it should be somewhere under 'public/stylesheets'.

Copy the above mentioned files into 'public/stylesheets/yui'.

## Create an application layout

I don't know about others, but every app I've done so far has had an application wide layout, not controller-specific ones.

There's a lot of possible choices you can make with grids.css, so I'm just going to toss something out there. You can take it, or toss it right back. Then I'll step through the interesting bits, and how you may want to vary it up.

Here's one my sample 'app/views/layouts/application.rhtml':

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
           "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
    <head>
      <meta http-equiv="content-type" content="text/html;charset=UTF-8" />
      <title>Something something : <%= controller.action_name %></title>
      <%= stylesheet_link_tag 'yui/reset', 'yui/fonts', 'yui/grids', 'yui/base',
        'scaffold', 'application' %>
    </head>
      <body>
        <div id="doc3" class="yui-t5">
          <div id="hd">
            <h1>Header</h1>
          </div>
          <div id="bd">
            <div id="yui-main">
              <div class="yui-b">
                <% if flash[:notice] %>
                  <p style="color: green"><%= flash[:notice] %></p>
                <% end %>
                <%= yield  %>
              </div>
            </div>
            <div id="yui-side" class="yui-b">
				Sidebar
            </div>
          </div>
          <div id="ft">
            Footer
          </div>
        </div>
      </body>
    </html>

### XHTML

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
           "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

I dig xhtml. Don't you?

If you use this, or HTML in a strict mode, you need to be particularly careful about closing your elements. If you don't, you might get things like the sidebar showing up at the bottom above the footer.

When this happens, I save the page to disk, and use 'xmllint --format /path/to/file'. If tags aren't properly closed, it should give you some clues as to which ones are the culprits. 'xmllint' comes from dev-libs/libxml2, and seems to come installed by default on Ubuntu

### Import the stylesheets

      <%= stylesheet_link_tag 'yui/reset', 'yui/fonts', 'yui/grids', 'yui/base',
        'scaffold', 'application' %>

Here we use a the stylesheet\_link\_tag to import the necessary stylesheets. These paths are relative to 'public/stylesheets'.

The first four stylesheets are our wonderful YUI stuff. The order here is important, as they are layered on top of the previously declared styles. Putting reset at the end, for example, would leave you with a pretty bland page.

'scaffold' is the scaffolding stylesheet that rails generates with 'script/generate scaffold.'  It provides a few styles that come in handy, like highlighting form fields with errors on it.

'application' is my application specific styles. I chose this to be analagous to 'public/javascripts/application.js' that can get loaded by default for you.

### Choosing the page width, and sidebar location and width

        <div id="doc3" class="yui-t5">

This is the where the magic begin. The id on this first div indicates the page width. This particular one does a 100% fluid layout. Possible values are 'doc', 'doc2', 'doc3', and 'doc4'.

The class indicates where the sidebar should be placed (regardless of source), and how wide it should be. This one is 240 pixels on the right. Possible values are 'yui-t1' through 'yui-t6'.

You might want to check out the [examples](http://developer.yahoo.com/yui/examples/grids/) to get a feel for how the different combinations can look.

### The header

          <div id="hd">
            <h1>Header</h1>
          </div>

This one is pretty simple. Put what you want in your header here. When doing up your 'application.css', among other things, you might want to give this some padding and a set height.

### The body and sidebar

          <div id="bd">
            <div id="yui-main">
              <div class="yui-b">
                <% if flash[:notice] %>
                  <p style="color: green"><%= flash[:notice] %></p>
                <% end %>
                <%= yield  %>
              </div>
            </div>
            <div id="yui-side" class="yui-b">
				Sidebar
            </div>
          </div>

The 'yui-b' class indicates the the div is a block of content. You need to wrap your primary block with a div\#yui-main. Any additional column come after this yui-main div.

The sidebar doesn't actually need an id. I just put it there to make it easier to target with selectors.

One minor point that always bugged me about the generated layout... I added an if around the flash\[:notice\], because otherwise it ends up printing an empty paragraph element.

It's important to note that your sidebar would always come after your main content div. The template magic above, ie yui-t5, does magic to make sure it appears correctly.


### The footer

          <div id="ft">
            Footer
          </div>

This is pretty straight forward too. Footer goes here.

## Wrapping it up, B

With that, we have a very basic application layout. At this point, I usually spend a few minutes adding a dash of color, and adding some padding here or there. If you're designerly inclined, you can spend a lot more time, but for developer like me, this is enough to get cranking on the heart and sole of the app.
