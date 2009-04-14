--- 
title: Updated Java System and Java 1.5 unmasked
tags: 
- gentoo
- java
layout: post
---

			<p>The Gentoo/Java Team is proud to announce the release of the updated Java system. This means that Java 1.5 is finally unmasked.</p>

<p>It is currently in ~arch. To begin using it, follow the upgrade guide:</p>

<p><a href="http://www.gentoo.org/proj/en/java/java-upgrade.xml">http://www.gentoo.org/proj/en/java/java-upgrade.xml</a></p>

<p>Here are some highlights of the new system</p>

 <p>* Ability to switch the current VM on the fly<br>
 * Changes to the user and system VM take effect immediately, and no longer are tied to the shell environment, which means you no longer have to run env-update followed by source /etc/profile when you switch the system VM<br>
 * Now has the concept of a "build VM", which is used to emerge packages, and is configured independently of the system VM<br>
 * For each version of Java, ie 1.3, 1.4, 1.5, etc, the build VM can be configured as to which vendor and version of a VM to use<br>
 * The VM at emerge time will be switched on the fly according to its configuration, as well as the dependency of the package. For example, some packages won't compile with 1.5. In these cases, a 1.4 VM will be used at build time<br>
 * Java packages which build with ant will have their build.xml rewritten at build time in order to ensure that the correct version of Java bytecode is compiled<br>
 * Java 1.5 has been unmasked, and we will be ready for Java 1.6 when it is released this fall.</p>

					
