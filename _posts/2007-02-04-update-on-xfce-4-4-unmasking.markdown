--- 
title: Update on Xfce 4.4 unmasking
tags: 
- gentoo
- xfce
layout: default
---

			<p>To follow up my previous post about unmasking Xfce 4.4...</p>

<p>Since then, I went through the process of upgrading from 4.2 to 4.4. Found a few kinks, mostly with files collisions caused by files moving from one package to another. These have been since worked out.</p>

<p>The major roadblock now is a number of broken dependencies for a few archs. These issues were conveniently glossed over while things are masked, but making repoman pretty upset the second they are unmasked.</p>

<p>So right now, all these issues have bugs filed for them, and now we play the waiting game. See the  <a href="https://bugs.gentoo.org/show_bug.cgi?id=163143">tracker</a> for the latest breaking news.</p>					