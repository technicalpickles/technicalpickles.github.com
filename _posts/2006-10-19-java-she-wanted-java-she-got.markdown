--- 
permalink: /posts/java-she-wanted-java-she-got.html
title: Java she wanted, Java she got
tags: 
- gentoo
- java
layout: post
---

			<p>It is with great pride that I am able to announce the 'new' Java system is now stable on all archs where we support Java! That means amd64, ia64, ppc, ppc64, and x86.</p>

<p>In case you've been stuck in a cave somewhere, and hadn't heard anything about the new system, here are some features highlights:</p>

<ul>
  <li>Changes to user and system VM happens instantly. No more need to run <code>env-update &amp;&amp; source /etc/profile</code>!</li>
  <li>One-time changes to the VM by using GENTOO_VM, ie <code>GENTOO_VM=kaffe ant</code> would run ant using kaffe, instead of the user or system VM</li>
  <li>Packages no longer depend on the system VM being set properly. This means that an  appropriate VM will be used for building for packages. A package needs 1.5 or later? No sweat! Only builds with 1.4? You got it!</li>
  <li>Now you can use all the Java 1.5 goodness you want with impunity!</li>
  <li>We'll be able to support Java 1.6 when it comes out in Decemeber in a much more reasonable amount of time.</li>
  <li>Support for configuring your Java browser plugin using eselect.</li>
  <li>Support for configuring your VM using eselect.</li></ul>

<p>While this may seem like a small list, it is a significant improvement over the old way of things. And this list is of user-facing changes... there are also vast improvements which help a lot of the developer side of things (ie making things much more maintainable).</p>

<p>[edit]<br>
On a side note, if you have been using the new system for a while, there are a few things you may want to do:
</p><ul>
  <li>Make sure you're not using the old migration-overlay. This will cause some problems if you try to emerge VMs among other things.</li>
  <li>Remove the package.mask entries if you had used them</li>
  <li>Remove the package.keywords entries if you had used them</li></ul>

<p>In other news... I've been always been a bit quiet on the blogging... but, I hope to change that, at least a little bit. I have some ideas for some writeups to show off Gentoo as a Java development platform... so stay tuned <img src="http://planet.gentoo.org/developers/rsc/smilies/icon_biggrin.gif" alt=":D" class="middle"></p>					