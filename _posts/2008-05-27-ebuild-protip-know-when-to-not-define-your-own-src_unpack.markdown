--- 
title: "Ebuild Protip: Know when to NOT define your own src_unpack"
tags: 
- ebuild
- ebuild protip
- gentoo
layout: post
---
I've often seen people submit ebuilds with `src_unpack()` like:

    src_unpack() {
    	unpack ${A}
    	cd ${S}
    }
    
This is not needed because there is a default `src_unpack()` that effectively does this for you. Take a look at `/usr/lib/portage/bin/ebuild.sh` and see for yourself.

__Update:__ [Diego](http://blog.flameeyes.eu/) pointed out one case where you would want to override `src_compile` with the default: you inherit an eclass that provides a `src_compile` that you don't want to be used. This is probably a pretty rare case though.
