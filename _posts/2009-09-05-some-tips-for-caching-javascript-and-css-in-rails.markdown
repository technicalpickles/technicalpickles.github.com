---
layout: post
---
There are a few tools out there that help benchmark your application's client side performance, most notable [YSlow](http://developer.yahoo.com/yslow/) and [PageSpeed](http://code.google.com/speed/page-speed/). They identify quite a few pointers for improving performance, but I'm going to focus on just one.

You generally want to [minimize round-trip times](http://code.google.com/speed/page-speed/docs/rtt.html#CombineJSandCSS) by [combining external javascript files](http://code.google.com/speed/page-speed/docs/rtt.html#CombineJS) and [combining external css files](http://code.google.com/speed/page-speed/docs/rtt.html#CombineCSS)

Imagine you're using [jQuery](http://jquery.com/) and maybe 2 plugins in addition to your own application's javascript. That's 4 extra trips the browser has to do to load your page. If you have a 2 style sheets, that's 2 more files, bringing us up to 6 trips.

What if there was a way to automatically combine the javascript and stylesheets files down into a single file a piece? Well guess what, Rails 2.0 and on do this very thing.

Take a gander at the documentation for [`javascript_include_tag`](http://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#M001713) and [`stylesheet_link_tag`](http://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#M001719). Scroll down a bit and you'll see a section on caching. The short version of the story is they both accept a `:cache` option, which will combine the files into the file you specify. This only happens when `ActionController::Base.perform_caching = true`.

Easy enough right? Let's dig in.

## Enable caching for development

`ActionController::Base.perform_caching` should be already set to true for production, so that's cool. However, it is off by default for development. You're almost definitely going to want to test this out in development to make sure everything works and looks correctly.

There should be a line like this in `config/environments/development.rb`

    config.action_controller.perform_caching             = false
    
Go ahead and flip it to true while we work this out.

    config.action_controller.perform_caching             = true
    
## Cache Javascript

I have a line like this in my `app/views/layout/application.html.haml`:

    = javascript_include_tag 'jquery.js', 'jquery-ui.js', 'jrails.js', 'application.js'
    
Note: you'll need to have all the files you want cached to be part of a single `javascript_include_tag`.
    
I'm going to have this cached into a `javascripts/cached/all.js`. I like keeping them in a cached directory, because it makes cleanup a bit easier. Here's the cached version:

    = javascript_include_tag 'jquery.js', 'jquery-ui.js', 'jrails.js', 'application.js', :cache => 'cached/all'

Now try clicking around your site. Take a gander at the generate HTML, and you should now see only one script tag for `/javascripts/cached/all.js`.

If you have to change your javascript, you'll have to clear out the cache. It's as easy as `rm -r public/javascripts/cached`.

### Gotchas

The only gotcha I've seen with caching Javascript is that [TinyMCE](http://tinymce.moxiecode.com/) did  not like to be cached. You probably don't want to be loading that unless you need to anyways.

## Cache CSS

Here's my original `stylesheet_link_tag` from my layout:

    = stylesheet_link_tag 'reset', 'layout', 'sidebar', 'imagereplacement', 'application.css', :media => 'all'
    
I want this cached to `/stylesheets/cached/all.css`. With caching, this ends up as: 

    = stylesheet_link_tag 'reset', 'layout', 'sidebar', 'imagereplacement', 'application.css', :media => 'all', :cache => 'cached/all'
    
Now do the clicking around your site again.

If change your stylesheets, you'll have to clear out the cache. It's as easy as `rm -r public/stylesheets/cached`.

### Gotchas

I found CSS quite a bit harder to get cached initially than javascript. Here's a quick list of things to look out for:

 * Avoid using @imports. You're trying to get everything into the same file, so they kind of defeat the purpose
 * Watch out for relative url() in your rules, because now your stylesheets will live in a different directory.
 * If you have multiple css media types (ie screen vs print), you'll have to do separate caches for each of them.
 
## Cached out

Hopefully by now, you're caching is all happy and good. You'll probably want to disable caching again in `config/environments/development.rb`, since it's kind of a drag for development:

    config.action_controller.perform_caching             = false
