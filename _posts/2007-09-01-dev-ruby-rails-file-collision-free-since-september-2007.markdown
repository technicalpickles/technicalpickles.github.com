--- 
permalink: /posts/dev-ruby-rails-file-collision-free-since-september-2007
title: "dev-ruby/rails: File Collision Free Since September 2007!"
tags: 
- eselect
- gentoo
- rails
layout: post
---
For some time now, there has been a [file collision](https://bugs.gentoo.org/show_bug.cgi?id=156092) between rails 1.1.x and rails 1.2.x, because it is a) slotted so both can be installed at the same time and b) both install /usr/bin/rails.

This issue has single handedly kept [rails-1.2.3 from being stablized](https://bugs.gentoo.org/show_bug.cgi?id=177209) until now.

Since they are battling over /usr/bin/rails, it made sense for each slot to install versioned rails script, like /usr/bin/rails-1.2.3, and then use [eselect](http://www.gentoo.org/proj/en/eselect/) to manage what /usr/bin/rails points at. This is a pretty common use case for using eselect.

[Hans de Graaff](http://moving-innovations.com/blog/), a fellow Gentoo Rubyist, did the initial work getting a new eselect module going. After reviewing and discussing it, we found a few issues that needed to be addressed. I took the torch and ran, and made sure most of the upgrade paths would worked properly.

So without further ado, I present you with dev-ruby/rails-1.1.6-r2 and dev-ruby/rails-1.2.3-r1.

These were barely committed, it might take a few hours to initially hit the mirrors.

Just 'emerge --u rails', and you'll get the updates.

Note the use of '-u'. This will ensure that both slots get updated at the same time (if they are installed) [without an annoying blocker](http://planet.gentoo.org/developers/zmedico/2007/08/19/using_blockers_to_adjust_merge_order).

Thanks goes out to [Hans de Graaff](http://moving-innovations.com/blog/) for his initial work and subsequent feedback, and [Zack Medico](http://planet.gentoo.org/developers/zmedico.php) for his initial post about sane upgrade paths with blockers, and subsequent help with figuring out how to get it to work with rails and eselect-rails.
