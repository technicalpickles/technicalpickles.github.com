--- 
title: Managing RubyGems on Gentoo
tags: 
- gems
- gentoo
- rails
- ruby
layout: default
---
For Ruby on Gentoo, it has been asked how one should go about managing [RubyGems](http://www.rubygems.org/). You have a few options:

 * Use Gentoo packages exclusively
 * Use `gem` exclusively
 * Mix and match and pray

## Mix and match
 
Offhand, mixing and matching seems like a bad idea.

Doing so, you might install a gem using portage, but then you could use `gem update` to update it. Then you update it with portage...

Overall, seems like it could be problematic in the long run.

## Gentoo packages exclusively

Being a Gentoo packager, my first take is that using packages is the way to go.

Some pluses:

 * One stop for installing/updating packages, ruby or otherwise
 * For gems with native extensions, can have dependencies on the things the native extensions use
 
Of course, there are a number of disadvantages.

 * There are tons of gems out there
 * Some update frequently
 * Lot of packages to be keeping up with stable keywords
 * Despite gem ebuilds being pretty straightforward, there is a bit of grunt work involved on to verify dependencies are accurate
 * Lots of gems are unpackaged, because they are usually packaged as requested or as developers need them
 * Despite being able to install multiple gems at once, packages usually aren't slotted that way


## Gems exclusively

If you're a Ruby developer, the most natural way would be using RubyGem directly. Some nice things include:

 * Most direct way to have the latest and greatest gems
 * Can have as many versions installed as you want
 * Zero maintenance for Gentoo packagers (sweet!)
 
Of course, not without their drawbacks:

 * If a gem uses native extensions, it can be tricky to determine what the it's dependencies are
 * Problematic when other (non-ruby) packages need to depend on gems
 
## My current practices

So where does that bring us?

Here are the practices I've taken to using for my Ruby and Rails development and deployment needs.

### In development

On my development machine (4 MacBook running Leopard with Gentoo/Prefix), I've using RubyGems directly.

If I'm working on a Rails app, and it needs any gems, I've taken to [vendoring everything](http://errtheblog.com/posts/50-vendor-everything). This type of thing will be [supported out of the box for Rails 2.1](http://ryandaigle.com/articles/2008/4/1/what-s-new-in-edge-rails-gem-dependencies).

Of course, this wouldn't work so well for things with native extensions, like [hpricot](http://code.whytheluckystiff.net/hpricot/).

### In production

I avoid installing any gems in production as much as possible. If an app needs something, it should be vendored, like I just described.

For gems needed by my apps that have native extensions, or are otherwise needed in production (like [rake](http://rake.rubyforge.org/) and the mysql gem), again, I'm using RubyGem directly.