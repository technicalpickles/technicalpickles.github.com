--- 
title: "Ebuild Protip: Only fetch from SRC_URI "
tags: 
- ebuild
- ebuild protip
- gentoo
layout: post
---
Normally, when `emerge` or `ebuild` go to download files, they will try to download from the [Gentoo mirrors](http://www.gentoo.org/main/en/mirrors2.xml) first.

Sometimes, you want to avoid this. There's two scenarios:

 * You're developing the ebuild, so the files won't be on the mirrors (yet), so you want to save yourself the time spent checking the mirrors
 * Something necessitates _always_ using the SRC\_URI source explicitly.
 
### First scenario

To elect to not use the mirrors on a command by command basis, you can do:

<pre class="terminal unix"><samp class="shell prompt">$</samp> <kbd class="shell">GENTOO_MIRRORS="" ebuild foo-1.2.3.ebuild manifest</kbd></pre>

Typically, you define `GENTOO_MIRRORS` in `/etc/make.conf`. We can temporary set it to nothing when we invoke `ebuild`, which results in the `SRC_URI` being used first.

### Second scenario

To prevent the ebuild from ever being populated to the mirrors, you need to add the following to your ebuild:

    RESTRICT="mirror"

Technically, this can go anywhere in the file. However, the common convention seems to be to put it after `IUSE`.
