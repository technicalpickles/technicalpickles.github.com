---
title: Rails Permanent Redirects
layout: post
tags:
- rails
---

I feel silly writing something so short and simple, but horribly outdated information when googling 'rails permanent redirect' drove me to it. I'm just hoping the SEO gods smile upon me.

So, my friends, check it:

    redirect_to "http://somewhere.com", :status => 301
    redirect_to posts_url, :status => :moved_permanently

For the curious, `:status` "can either be a standard [HTTP Status code](http://www.iana.org/assignments/http-status-codes) as an integer, or a symbol representing the downcased, underscored and symbolized description" ([source](http://api.rubyonrails.org/classes/ActionController/Redirecting.html#method-i-redirect_to))
