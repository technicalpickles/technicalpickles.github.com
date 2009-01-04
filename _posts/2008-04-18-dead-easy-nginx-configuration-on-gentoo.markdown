--- 
title: Dead easy nginx configuration on Gentoo
tags: 
- gentoo
- nginx
- rails
- ruby
layout: default
---
Considering the hype about [nginx](http://nginx.net/) in the Rails community, I figured it was about time to try it out.

Being the slacker that I am, I didn't really want to have to learn how to use it. Fortunately, [Ezra](http://brainspl.at/) gives us a [good starting point for a configuration](http://brainspl.at/articles/2006/08/23/nginx-my-new-favorite-front-end-for-mongrel-cluster).

Then [Chris Wanstrath](http://ozmm.org/) comes along and creates [nginx_config_generator](http://errtheblog.com/posts/52-nginx-config-like-whoa) to make it even easier, by providing a generator around this configuration.

One kink though. Running on Gentoo and using the [latest nginx package](http://packages.gentoo.org/package/www-servers/nginx). Default template has paths not suitable for this. Bummer.

Being the clever fellow he is, Chris's generator does provide a way around this, by letting you specify your own template to use.

I took a little time to tweak his original template for Gentoo consumption. [Behold!](http://p.caboo.se/182778.txt)

Here's how you'd use it, _assuming you've gone through the steps already mentioned in the [post](http://errtheblog.com/posts/52-nginx-config-like-whoa)_.

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">wget http://p.caboo.se/182778.txt -O gentoo_nginx.conf.erb</kbd>
<samp class="prompt shell">$</samp> <kbd class="shell">generate_nginx_config config.yml nginx.conf \
      --template gentoo_nginx.conf.erb</kbd></pre>

You should be all set now.

One last note, you need to set `fair: false` for your sites. The `fair` directive doesn't seem to work with the current version we have on Gentoo. I'm not positive, but I think it's related to [this post by Ezra](http://brainspl.at/articles/2007/11/09/a-fair-proxy-balancer-for-nginx-and-mongrel)