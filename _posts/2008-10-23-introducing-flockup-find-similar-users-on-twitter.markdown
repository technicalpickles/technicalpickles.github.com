--- 
permalink: /posts/introducing-flockup-find-similar-users-on-twitter
title: "Introducing FlockUp : Find Similar Users on Twitter"
tags: 
- rails
- railsrumble
- ruby
- twitter
layout: post
---
This past weekend, [Wyatt Greene](http://twitter.com/explodingtrees) and [I](http://twitter.com/techpickles) participated in [Rails Rumble 2008](http://railsrumble.com/). If you're not familar with it, here's a quick summary:

> Think you have what it takes to embrace your constraints and build a kick-ass web startup in a mere 48 hours? If so, the Rails Rumble might be for you!
>
>Spend a weekend with us, designing, developing, and deploying the micro application you've been dreaming about. And in case the launch isn't enough reward in and of itself, our kind event sponsors have offered up a bunch of really kick ass prizes that the community will help award to the best new web properties.

How about I tell you a little bit about our entry?

### The pitch

[Twitter](http://twitter.com) is a big place. A real big place. When you first join, it can be a little overwhelming. Who do you follow? How do you find other twitterers with similar interests?

[FlockUp](http://flockup.com) is here to help you answer these questions. Here, you can:

 * Create a flock. Examples include:
  * A topic of interest: [ruby](http://flockup.com/flocks/ruby)
  *  An event: [webinno20](http://flockup.com/flocks/webinno20)
  * A location: [boston](http://flockup.com/flocks/boston)
 * Add 'flockers' to a flock. This just means adding a twitter user to a 'flock'. Some examples
  * Josh Nichols is really interested in [ruby](http://flockup.com/flocks/ruby)
  * Wyatt Greene is participating in [railsrumble](http://flockup.com/flocks/railsrumble)

FlockUp is of course not affiliated with Twitter. We just happen to like it! FlockUp is experimental software, offered free of charge from the generosity of our hearts.

### Walking through

We built the front page to reflect that we're trying to help you find people on twitter. Most prominent on it? The search:

![FlockUp front page](http://img.skitch.com/20081020-mmqye4mj8gr2iniws526ma5bq8.jpg)

I'll go ahead and search for some Ruby action:

![FlockUp search results](http://img.skitch.com/20081020-q94m9jt422xqyfmp96hmnk9ygp.jpg)

We have a few types of results here. The first few are 'flocks'. This is really just a group. The rest are flockers. A flocker is someone that is on twitter that FlockUp knows about. Let's click through to the ruby flock...

![Viewing Flock ruby](http://img.skitch.com/20081020-qsgr8k13dywjf3iqtpgmwphqq5.jpg)

This should be pretty straightforward. We have a brief description about the flock, and then a list of the flockers. We also have a way to add someone to the flock.

We tried it really easy to add to a flock. No login, no captcha. Just type and click. After you enter someone, we do a little verification, and then tell them they've been added. Here's an [example of the tweet they get](http://twitter.com/flockup/statuses/966613727).

If the person clicks through the link, they'll be greeted with this:

![Viewing Flocker techpickles](http://img.skitch.com/20081020-bj8q3fimu8y5137ixa8sfjq7hw.jpg)

### GO GO Presidential Vampire League!

Like what you see? We would be honored if you showed your support by voting for us. [Here is our team page](http://railsrumble.com/teams/the-presidential-vampire-league). Voting is expected to open up in the next day or so.
