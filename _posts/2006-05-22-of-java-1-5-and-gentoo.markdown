--- 
title: Of Java 1.5 and Gentoo
tags: 
- gentoo
- java
layout: default
---

			<p>The road to unmasking Java 1.5 has been long and arduous. There have been enough issues to warrant it's own <a href="http://www.gentoo.org/proj/en/java/tiger-faq.xml">FAQ</a> and an entire redesign of the way we handle Java packages.</p>

<p>I won't go into detail about the specific problems incurred, as the FAQ should sufficiently cover them.</p>

<p>Nearly a year ago, work was begun on a new build system (ie new eclasses and a new version of java-config). About six months later, we had a new fully featured, flexible build system. Highlights include build-time VM switching, build-time build.xml rewriting, and a more flexible means of storing/setting the system and user VM.</p>

<p>Unfortunately, there were enough incompatibilities between this new system and the old one that we couldn't just drop it in the tree as is. A few months were needed to determine how to best migrate to the new system, in such a way that it could be done incrementally, and it could be properly tested (ie be in ~arch). Notes about the method we choose can be found on our <a href="https://projects.gentooexperimental.org/expj">wiki</a>.</p>

<p>Since then, a lot of work has been done to implement the migration system, as well as code and documentation cleanup.</p>

<p>So now, we (kartlk and I), have deemed the new system ready for prime time. This coming week, we will be finishing any last minute cleanups, and bringing in any packages that require 1.5. Once that is complete, we will be announcing the changes on the developer mailing for public scrutiny/input. Assuming that there are no issues that crop up, the new system would be brought into the main tree a week or so later.</p>

<p>You may begin rejoicing now.</p>					