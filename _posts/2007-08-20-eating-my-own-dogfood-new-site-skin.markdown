--- 
permalink: /posts/eating-my-own-dogfood-new-site-skin.html
title: "Eating My Own Dogfood: New Site Skin"
tags: 
- css
- yui
layout: post
---
When I first did the skin for this site, I only was using [YUI](http://developer.yahoo.com/yui/)'s [reset](http://developer.yahoo.com/yui/reset/) and [fonts](http://developer.yahoo.com/yui/fonts/) libraries. I'm not even why I didn't use the [grids](http://developer.yahoo.com/yui/grids/) library, but I opted to do it myself (well, almost myself, I yanked a lot of the markup from another project I had been working on at the time). This was also before the [base] library had been released.

So I broke down and did a bit of refactoring and did up this new appearance. I'm using the technique I laid out in [this earlier post](/blog/permalink/up-and-running-with-rails-and-yui-css.html).

The result is that the markup is much cleaner, and I ended up with a lot less CSS in my application.css. Good things, to be sure.

The only thing I'm not really happy with at the moment is my navigation menu. I had hoped to use YUI's [menu component](http://developer.yahoo.com/yui/menu/), but I wasn't able to quite get it working on my first pass, so I settled with what you see now.

As always, feedback is always welcome.
