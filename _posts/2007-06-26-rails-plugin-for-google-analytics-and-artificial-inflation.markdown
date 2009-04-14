--- 
title: Rails plugin for Google Analytics and artificial inflation
tags: 
- google analytics
- rails
layout: post
---
Being the budding webmaster / blogger that I am, it comes natural to want to gather as much information about the traffic coming to my site. The obvious response to this is, duh, [Google Analytics](http://google.com/analytics).

If you were to guess that someone has taken the effort to write a Rails plugin for Google Analytics, you'd be quite right. You can find out all about it [here](http://www.rubaidh.com/projects/google-analytics-plugin/).

After using it a while, I started to notice that my hits seemed a bit artificially high. I thought about it a bit, and realized that it was probably caused by me, when I would log in, write a post, and generally make sure things are working.

I have a `authorized?` method that returns true if logged into a user, so ideally, I would skip adding the google magic when that's true. Here's the solution I came up with.

First, we want to skip the `before_filter` that the plugin adds. In `app/controllers/application.rb`, I add:

    skip_after_filter :add_google_analytics_code

Now I create a new filter that will only do the google magic when viewed by a non-user.

    after_filter :add_google_analytics_unless_authorized
    def add_google_analytics_unless_authorized
      add_google_analytics_code unless authorized?
    end

I also didn't want stats collected for my user controller, like when I first try to login, so in `app/controllers/users_controller.rb`, I added:

    skip_after_filter :add_google_analytics_unless_authorized

And that's about all there is to it.
