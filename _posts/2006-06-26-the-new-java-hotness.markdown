--- 
title: The New Java Hotness
tags: 
- gentoo
- java
layout: post
---

I'm pleased to announce that the new Java hotness has finally hit the tree <img src="http://planet.gentoo.org/developers/rsc/smilies/icon_smile.gif" alt=":)" class="middle"> It's currently in package.mask, but I expect to unmask it in the next few days.

To begin using it, you will need to add the appropriate entries to /etc/portage/package.unmask, and then follow the <a href="http://www.gentoo.org/proj/en/java/java-upgrade.xml">upgrade guide</a>.

For those not familar with that this means, here are some highlights:

* Ability to switch the current VM on the fly
* Changes to the user and system VM take effect immediately, and no longer are tied to the shell environment (ie no more running env-update &amp;&amp;
* e /etc/profile after switching the sytem VM)
* Now has the concept of a *build VM*, which is used to emerge packages, and is configured independently of the system VM.
* For each version of Java, ie 1.3, 1.4, 1.5, etc, the build vm can configured as to which vendor and version of a VM to use
* The VM at emerge time will be switched on the fly according to its configuration, as well as the dependency of the package. For example, some packages won't compile with 1.5. In these cases, a 1.4 VM will be used at build time.
* Java packages which build with ant will have their build.xml rewritten at build time, in order to ensure that the correct version of Java bytecode is compiled.
* We'll be able to unmask Java 1.5 soon, and be able to handle Java 1.6 when it comes out this fall.
