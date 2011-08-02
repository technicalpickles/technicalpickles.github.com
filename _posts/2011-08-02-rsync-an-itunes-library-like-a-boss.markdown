---
title: Rsync an iTunes Library like a boss
layout: post
tags:
- mac
- itunes
- rsync
---

Motivation
----------

Shiny new MacBook Air, and I wanted to do this install from scratch (no TimeMachine recovery), but still get the good stuff from my old laptop. Also, rsync is awesome.

Prepare old computer
---------------------

On old computer, from iTunes: `File ->  Library -> Organize Library` and check `Consolidate files`.

After this looks like it finished (would be displayed in status area, i.e. song progress), **quit iTunes**.

In `System Preferences`, go to `Sharing` and make sure `Remote Login` is enabled.

Make note of this computer's IP address. You can use `/sbin/ifconfig` or the `Network` preferences.


Prepare new computer
----------------------

**Quit iTunes.**

Backup existing files, just in case:

    cd ~/Music
    mv iTunes iTunes.bak

rsync in boss like fashion:
----------------------------

Remember that IP address from earlier? Let's pretend it's 192.168.1.4 for demonstration.

On new computer:

    cd ~/Music
    rsync -av --delete 192.168.1.4:Music/iTunes .

Now wait, and have a glass of scotch.

The result
-----------

Open iTunes on your new computer, and you should have all these things:

 * Music
  * Playlists
  * Song plays
  * Ratings
 * Videos
 * Syncability with iPhones/iPods

Troubleshooting
---------------

The first time I tried this, it didn't recognize anything in the Library. Not sure if these matter, or if the second run was just luckier. In any event, these are things I tried, and are reflected above when it's pretty likely to have helped:

 * Tried to add existing Library to itself. Cancelled partway through
 * On old computer, exported Library using `File -> Library -> Export Library`
 * Added --delete to rsync run
 * Made sure iTunes wasn't running during rsync
