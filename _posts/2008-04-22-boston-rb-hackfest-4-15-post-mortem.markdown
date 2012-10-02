--- 
title: Boston.rb Hackfest 4/15 Post Mortem
tags: 
- boston
- css
- haml
- rails
- ruby
- shoulda
layout: post
---

Last Tuesday, we had a small, but dedicated showing at the [Hackfest](http://upcoming.yahoo.com/event/473163/):

 * [Dan Croak](http://dancroak.com)
 * Tom  Shealy
 * [Josh Nichols](http://technicalpickles.com)

Going in, we didn't really have a something in mind to work.

We thought to pick up the app we started in [merb](http://www.merbivore.com/) for organizing [katas](http://groups.google.com/group/boston-rubygroup/browse_thread/thread/3fb314df0344cb4b/6a8fb0b6df1d2c7b?lnk=gst&q=kata#6a8fb0b6df1d2c7b). But when it came down to it, we figured it'd be a lot quicker if we just did it [rails](http://rubyonrails.org/).

We actually wanted something a little more general, kind of a one stop shop for Boston Ruby stuff. Here's what we wanted:

 * Keep track of events (meetings, hackfests, and katas)
 * Projects that have come out of above-mentioned meetings
 * Recent commits from these projects
 
Tools we used:

 * [rails](http://rubyonrails.org/)
 * [shoulda](http://thoughtbot.com/projects/shoulda)
 * [haml](http://haml.hamptoncatlin.com/)
 * [blueprint css](http://code.google.com/p/blueprintcss/)
 * [feed-normalizer](http://code.google.com/p/feed-normalizer/)

You can see the code we ended up with in the [hackfest repository](https://svn.thoughtbot.com/hackfest/boston_rb).

Or, you can [see it in action](http://bostonrb.org).

For simplicity's sake, we didn't initially bother with users/permissions/etc, so be gentle with it.
