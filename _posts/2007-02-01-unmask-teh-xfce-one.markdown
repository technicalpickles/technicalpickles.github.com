--- 
title: unmask teh Xfce!!!one
tags: 
- gentoo
- xfce
layout: default
---

			<p>The question of when Xfce 4.4 will be unmasked has been coming up with increasing frequency.</p>

<p>The default answer is... wait, for it, when it's ready <img src="http://planet.gentoo.org/developers/rsc/smilies/icon_mrgreen.gif" alt=":&gt;&gt;" class="middle"></p>

<p>A more elaborate answer follows:</p>

<p>We have established a 'tracker' bug <a href="https://bugs.gentoo.org/show_bug.cgi?id=163143">here</a>. The team has been pretty good about keeping on top of bugs that come up with it, as in, there are no known issues currently.</p>

<p>So that means it's ready, right? Not quite. Below are the outstanding issues I see:</p>

<p>In making haste to get a 0-day release of 4.4, some corners may have been cut. In particular, some of the xfce-extra packages are lacking the appropriate keywords, even though xfce-base/xfce4-extras depend on them. This is only for new xfce-extra packages, which have been added only relatively recently.</p>

<p>Icons seemed to have moved around a bit, as in, which packages owns them. This is a bit problematic, as it causes file collisions (enabled by FEATURES="collision-protect"). While not critical to unmasking, it is pretty key for stabilizing, and the sooner it can be fixed the better.</p>

<p>The other things I'm concerned about is the upgrade path from 4.2 to 4.4. Some things that were extras in 4.2 are now part of 4.4. As a result, some xfce-extra packages have blockers against them. In other cases, xfce-extra packages haven't been updated for 4.4, or otherwise are no longer maintained. These xfce-extra packages have blockers against them as well. The consequence of these point is that you will have to unmerge the packages that are reported as being blocked.
</p>					