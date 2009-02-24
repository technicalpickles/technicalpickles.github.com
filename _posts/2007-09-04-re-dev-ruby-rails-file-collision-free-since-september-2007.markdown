--- 
permalink: /posts/re-dev-ruby-rails-file-collision-free-since-september-2007
title: "RE: dev-ruby/rails: File Collision Free Since September 2007!"
tags: 
- eselect
- gentoo
- rails
layout: post
---
I wrote earlier about our ebuilds for Rails being [file collision free](/blog/permalink/dev-ruby-rails-file-collision-free-since-september-2007.html).

To get around having a blocker exposed to the user, rails had a post dependency on eselect-rails, meaning that eselect-rails would get merged AFTER rails. What I failed to remember, though, was that the eselect module was used during the merge of rails, so it had to be a build-time dependency, meaning it'd be pulled in before rails was merged.

This resulted in [bug #191015](https://bugs.gentoo.org/show_bug.cgi?id=191015).

Just changing it to DEPEND, and leaving the blockers in eselect-rails would have made annoying blockers, that would probably be a bit confusing for end-users.

So I did a bit of thinking about what really should block what. Versions less than 1.1.6-r2 are in SLOT=1 and install /usr/bin/rails. Versions 1.2.0, 1.2.1, 1.2.2, and 1.2.3 are in SLOT=2 and install /usr/bin/rails. So why not just make those versions block each other?

For less than 1.1.6-r2, we have:

    DEPEND=">=dev-lang/ruby-1.8.2
			-snip-
            !~dev-ruby/rails-1.2.0
            !~dev-ruby/rails-1.2.1
            !=dev-ruby/rails-1.2.3"

And in 1.2.0, 1.2.1, 1.2.2, and 1.2.3, we have:

    DEPEND=">=dev-lang/ruby-1.8.5
    		-snip-
            !&lt;dev-ruby/rails-1.1.6-r1"

I then removed the blockers in eselect-rails, so now it just depends on eselect.

So here are some use cases, and what happens you try to do emerge -u rails.

If you don't care about these, you can just skip down to the summary.

### rails-1.1.6-r1 installed

/usr/bin/rails is from rails-1.1.6-r1

    metaverse ~ # emerge -ptvu rails

    These are the packages that would be merged, in reverse order:

    Calculating dependencies... done!
    [ebuild     U ] dev-ruby/rails-1.1.6-r2 [1.1.6-r1] USE="fastcgi mysql -doc -postgres -sqlite -sqlite3" 0 kB 
    [ebuild  NS   ] dev-ruby/rails-1.2.3-r1  USE="fastcgi mysql -doc -postgres -sqlite -sqlite3" 0 kB 
    [ebuild  N    ]  app-admin/eselect-rails-0.10  0 kB 
    
    Total: 3 packages (1 upgrade, 1 new, 1 in new slot), Size of downloads: 0 kB

The version in SLOT=1 gets updated to the latest, and the version in the new slot gets brought in.

Version 1.2.3-r1 gets merged first. During its postinstall, it tries to update the /usr/bin/rails symlink, but it's a file at this point, so it ignores it.

Version 1.1.6-r2 gets merged next, then 1.1.6-r1 gets unmerged because it's in the same slot. This results in there being no /usr/bin/rails. When the postinstall runs, it sees this, and populates it to point use this version. 

/usr/bin/rails is a symlink managed by eselect-rails, and points at /usr/bin/rails-1.1.6

### rails-1.2.3 installed

/usr/bin/rails is from rails-1.2.3.

    metaverse ~ # emerge -ptvu rails
    These are the packages that would be merged, in reverse order:

    Calculating dependencies... done!
    [ebuild     U ] dev-ruby/rails-1.2.3-r1 [1.2.3] USE="fastcgi mysql -doc -postgres -sqlite -sqlite3" 0 kB 
    [ebuild  N    ]  app-admin/eselect-rails-0.10  0 kB 
    
    Total: 2 packages (1 upgrade, 1 new), Size of downloads: 0 kB

The version in SLOT=2 gets updated to the latest.

Version 1.2.3-r1 gets merged next, then 1.2.3 gets unmerged because it's in the same slot. This results in there being no /usr/bin/rails. When the postinstall runs, it sees this, and populates it to point use this version. 

/usr/bin/rails is a symlink managed by eselect-rails, and points at /usr/bin/rails-1.2.3

### rails-1.1.6-r1 and rails-1.2.3 installed

This can only happen if you don't have FEATURES="collision-protect" in your make.conf.

/usr/bin/rails ends up being from whichever version was last installed.

    metaverse ~ # emerge -ptvu rails
    These are the packages that would be merged, in reverse order:

    Calculating dependencies... done!
    [ebuild     U ] dev-ruby/rails-1.1.6-r2 [1.1.6-r1] USE="fastcgi mysql -doc -postgres -sqlite -sqlite3" 0 kB 
    [ebuild     U ] dev-ruby/rails-1.2.3-r1 [1.2.3] USE="fastcgi mysql -doc -postgres -sqlite -sqlite3" 0 kB 
    [ebuild  N    ]  app-admin/eselect-rails-0.10  0 kB 
    [blocks B     ] =dev-ruby/rails-1.2.3 (is blocking dev-ruby/rails-1.1.6-r1)

    Total: 3 packages (2 upgrades, 1 new, 1 block), Size of downloads: 0 kB

Now we have a blocker. What does it mean? It's saying that rails-1.2.3 is in the way of rails-1.1.6-r1. This is a little weird, because you'd think that since both versions are getting updated in such a way that would negate the blocker. It seems [a bug has been filed](https://bugs.gentoo.org/show_bug.cgi?id=172812) about this very thing.

In the mean time, this is how you resolve this:

    emerge -C =dev-ruby/rails-1.2.3

At this point, we only have rails-1.1.6-r1 installed, and it goes along like the first use case.

/usr/bin/rails is a symlink managed by eselect-rails, and points at /usr/bin/rails-1.1.6

## If the old reversions have a file-collision, and now block each other, how did you test these use cases?

I disabled collision-protect on the command line, like:

    FEATURES="-collision-protect" emerge ...

And I used the --nodeps command line argument for emerge, in order to ignore the dependencies, hence getting around the blockers.

The result is...

	FEATURES="-collision-protect" emerge --nodeps =rails-1.2.3 =rails-1.1.6-r1

*IMPORTANT* This is *not* how you should get around blockers / file collisions. It really should only be used for testing and development.

### Summary

The upgrade path ended up being a bit foobared for 1.1.6-r2 and 1.2.3-r1.

If you were seeing behavior described in [bug #191015](https://bugs.gentoo.org/show_bug.cgi?id=191015), I suggest doing:

    metaverse ~ # emerge -C rails eselect-rails
	metaverse ~ # emerge rails

If you see a blocker, that might look like:

    Calculating dependencies... done!
    [ebuild     U ] dev-ruby/rails-1.1.6-r2 [1.1.6-r1] USE="fastcgi mysql -doc -postgres -sqlite -sqlite3" 0 kB 
    [ebuild     U ] dev-ruby/rails-1.2.3-r1 [1.2.3] USE="fastcgi mysql -doc -postgres -sqlite -sqlite3" 0 kB 
    [ebuild  N    ]  app-admin/eselect-rails-0.10  0 kB 
    [blocks B     ] =dev-ruby/rails-1.2.3 (is blocking dev-ruby/rails-1.1.6-r1)

You need to...

    emerge -C =rails-1.2.3

