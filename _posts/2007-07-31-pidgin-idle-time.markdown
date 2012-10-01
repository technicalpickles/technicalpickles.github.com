--- 
title: Pidgin idle time
tags: 
- gentoo
- pidgin
- ubuntu
- use flags
layout: post
---
So I'd like [Pidgin](http://pidgin.im) to report idle time sanely on my [Gentoo](http://www.gentoo.org) box.

For the longest time on this install, I've only been able to choose 'Never' or 'From last message sent.' Neither of those seem to give an accurate sense of your idleness.

For me, idleness really is based on your being or not being at the computer, typing or mousing away.

After grumbling about it long enough, I finally started look for information about it, googling for 'gentoo pidgin idle'. The [first result](http://ubuntuforums.org/showthread.php?t=479862) was from the Ubuntu forums mentioning how you needed xscreensaver, and to recompile, to get the desired behavior.

Sure enough, pidgin hadn't been compiled with the xscreensaver USE flag. Remerging with it enabled did the trick.

I really wouldn't have guessed that xscreensaver would have provided this. It almost makes me think that just having these global use flag descriptions isn't enough; it'd be great to be able to know how a USE flag affects a particular package.
