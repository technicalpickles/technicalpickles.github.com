--- 
title: JAVA_HOME pointing to generation-1 system vm
tags: 
- gentoo
- java
layout: default
---

			<p>So, one of the issues that has come up on occaision is that JAVA_HOME ends up pointing at, for instance, /opt/blackdown-jdk-1.4.2.03, instead of the shiny, recently unmasked Java 1.5 you installed and set as your VM of choice.</p>

<p>The reason for this has is legacy support. java-config-1 isn't so smart. It determines what the current VM is based on JAVA_HOME in the environment. It expects it to be living under /opt/${P}, where ${P} is the name and version of the VM.</p>

<p>The workaround we found is described in the <a href="http://www.gentoo.org/proj/en/java/java-upgrade.xml#doc_chap6">upgrade guide</a>. However, the problem is, that java-config-1 would then no longer be able to know what VM is currently being used, which is a bit problematic.</p>

<p>Thanks to the hax0ring skills, we now have a patched java-config-1 that uses VMHANDLE instead of JAVA_HOME. VMHANDLE is the token by which a VM is identified with the new Java system, and happens to be in the environment.</p>

<p>We have added a new entry for /etc/profile.d, which uses the prescribed way of setting JAVA_HOME from the upgrade guide. This means that JAVA_HOME would be set correctly most of the time. The only time it wouldn't be accurate would be if you did not have a user vm set (JAVA_HOME points at /etc/java-config-2/current-system-vm), and then you set a user vm, JAVA_HOME would point at current-system-vm, instead of ~/.gentoo/java-config-2/current-user-vm. Running 'source /etc/profile' though, remedies the problem.</p>

<p>Perhaps java-config-2 can be updated to give notice that you need to source /etc/profile ? Or perhaps it can directly update JAVA_HOME... The only problem with the the latter point ist hat it would probably only fix JAVA_HOME in the current terminal, and not in all the other open terminals.</p>

<p>I've added revision bumps of java-config to the tree that work as described here. Because of the changes though, I've put them in package.mask while we finish testing the new way of handling JAVA_HOME. The package.mask entry is below, for those interested in testing and providing feedback:</p>

<pre># Joshua Nichols &lt;nichoj@gentoo.org&gt; (02 Aug 2006)
# Testing new versions of java-config, which will let us remove
# JAVA_HOME from env, which points to generation-1 system vm
=dev-java/java-config-1.3.0-r3
=dev-java/java-config-2.0.26-r6
</pre>

<p>Assuming there aren't any issues while testing, I expect to unmask it in a day or two. At that point, I'll also update all the env.d files for the VMs, so they no longer export JAVA_HOME (note: this is different from the profile.d, which is actually overwriting the JAVA_HOME originally being set by the env.d file).</p>					