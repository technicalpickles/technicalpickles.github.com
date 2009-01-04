--- 
title: Capistrano 2.0.0 on Gentoo at last
tags: 
- capistrano
- gentoo
- rails
layout: default
---
[Capistrano 2.0.0](http://www.capify.org) has been released for just over a month, but there hadn't been much activity for it on the [Gentoo front](https://bugs.gentoo.org/show_bug.cgi?id=187490).

The reason for this is that we had 1.4.x in the tree as SLOT=0, and the prelease 1.99.x also in the tree (but package.masked) as SLOT=0 as well. The real problem is that they were actually [intended by upstream to be installable at the same time](http://www.capify.org/upgrade/faq), so you could have projects on the same machine either version. The logical action would be to slot it accordingly, but we would run into the [same issues we had with rails](/blog/permalink/dev-ruby-rails-file-collision-free-since-september-2007.html) because of a file collision with both slots wanting to control /usr/bin/cap.

We had originally thought to do another eselect module again, but it turns out that upstream was one step ahead of us. /usr/bin/cap actually allows for running either version. Check it:

    nichoj@metaverse ~ $ cap --version
    Capistrano v2.0.0
    nichoj@metaverse ~ $ cap _1.4.1_ --version
    Capistrano v1.4.1
    nichoj@metaverse ~ $ cap _2.0.0_ --version
    Capistrano v2.0.0

What we ended up having to do was pull out /usr/bin/cap into a separate package, capistrano-launcher. We used appropriately [blockers](http://planet.gentoo.org/developers/zmedico/2007/08/19/using_blockers_to_adjust_merge_order) again to have a sane upgrade path.

I also had to do a slotmove of the existing versions, putting 1.4.x in SLOT=1 and 1.99.x into SLOT=2. This was as easy as updating the ebuilds, and adding the following to ${PORTDIR}/profiles/updates/3Q-2007:

    slotmove =dev-ruby/capistrano-1.4* 0 1
    slotmove =dev-ruby/capistrano-1.9* 0 2

Without further ado, make with the emerging.

	emerge -atv =capistrano-1* =capistrano-2* # get both versions
	emerge -atv capistrano # or just the latest
