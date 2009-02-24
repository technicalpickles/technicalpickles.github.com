--- 
permalink: /posts/summary-of-java-team-meeting.html
title: Summary of Java team meeting
tags: 
- gentoo
- java
layout: post
---

			<p><i>Cross posted to gentoo-dev and gentoo-java mailing list.</i>
</p><p>
Yesterday, the Java team held a meeting.
The agenda for the meeting is available at <a href="http://overlays.gentoo.org/proj/java/wiki/September_2006_Meeting_Outline">here</a>.
This summary follows the format of the agenda.
</p>

<ol>
<li><p><b>Personel updates</b></p>

<p>First task was to aquire information about active developers who work with Java
related ebuilds, it produced following list:</p>

  <ul>
   <li><p>betelgeuse (Petteri Räty)</p></li>
   <li>caster (Vlastimil Babka)</li>
   <li>gurligebis (Bjarke Istrup Pedersen)</li>
   <li>nelchael (Krzysiek Pawlik)</li>
   <li>nichoj (Joshua Nichols)</li>
   <li>sanchan (Sandro Bonazzola)</li>
   <li>wltjr (William Thomson)</li>
  <p></p></ul>

<p>More details about areas of responsibility are available in <a href="http://overlays.gentoo.org/proj/java/wiki/September_2006_Meeting_Notes">meeting
notes</a>.</p>

<p>Missing developers:</p>
  <ul>
   <li>compnerd (Saleem Abdulrasool)</li>
   <li>karltk (Karl Trygve Kalleberg)</li>
   <li>zx (Chris Aniszczyk)</li></ul>

<p>Second task was to establish a list of developers along with architectures with
which they can work:</p>

  <ul>
   <li>alpha - no support for Java</li>
   <li>amd64 - nichoj, sanchan</li>
   <li>arm - no support for Java</li>
   <li>hppa - no support for Java</li>
   <li>ia64 - nichoj</li>
   <li>m68k - no support for Java</li>
   <li>mips - no support for Java</li>
   <li>ppc - nelchael, nichoj</li>
   <li>ppc-macos - no support for Java</li>
   <li>ppc64 - nichoj</li>
   <li>s390 - no support for Java</li>
   <li>sh - no support for Java</li>
   <li>sparc - no support for Java</li>
   <li>x86 - all Java team developers</li>
   <li>x86-fbsd - gurligebis</li></ul>

<p>It was decided to drop support for dev-java/compaq-jdk and dev-java/compaq-jre
on alpha - those two packages don't work as expected and are not supported by
upstream. Sparc and alpha wait for fully working free Java JDK/JRE (kaffe, gcj
and gnu-classpath for example).</p>
<p></p>

</li><li><p><b>Migration to the new Java system</b></p>

<p>It was decided that no new features will be added to 'core' Java packages to
allow proper testing and bug fixing. We are targeting 14 October for stabilizing
core packages, list of core packages is available at <a href="http://www.gentoo.org/proj/en/java/java-upgrade.xml">Java Upgrade
Guide</a> under "Code Listing 2.1: package.keywords". There was also talk about
what features are planned to make their way into java-config before feature
freeze, for example --tools option to get location for tools.jar from currently
selected VM.</p>

<p>Current status of migration (list of not migrated ebuilds, progress graph) is
available at <a href="http://dev.gentoo.org/%7Enelchael/java-generation-2/">status
page</a> along with 'current' state at <a href="http://dev.gentoo.org/%7Enelchael/java-generation-2/not-migrated-current">not-migrated-current</a>.</p></li>

<li><p><b>Feature requests</b><br><br></p>

<p>Only one point made it's way into "Feature requests": virtuals for several Java
packages: javamail, jaf and others. To better understand the problem lets take a
look at packages that could provide virtual/javamail:</p>

  <ul>
   <li><p>dev-java/sun-javamail-bin</p></li>
   <li>dev-java/gnu-javamail</li>
   <li>dev-java/sun-javamail (currently in <a href="http://overlays.gentoo.org/proj/java/browser/">Java overlay</a>)</li>
  <p></p></ul>

<p>Same situation is with JAF (JavaBeans Activation Framework). Decision about
virtuals was postponed as minor issue, since it can wait until after the new
Java system is stablized.</p>
<p></p>

</li><li><p><b>Documentation</b></p>

<p>It was decided that the Java Upgrade Guide needs to be updated and it needs to
list possible upgrade paths. Additionally following documents need work:</p>

  <ul>
   <li><p><a href="http://www.gentoo.org/proj/en/java/tomcat-guide.xml">Tomcat Guide</a></p></li>
   <li><a href="http://overlays.gentoo.org/proj/java/browser/docs/resin-guide.xml">Resin
Guide (not published yet)</a></li>
   <li><a href="http://overlays.gentoo.org/proj/java/wiki/Common_Problems">Common
Problems</a></li>
   <li><a href="http://overlays.gentoo.org/proj/java/wiki/How_to_be_a_good_upstream">How
to be a good upstream</a></li>
  <p></p></ul><br>

 <p>Following documents need to be created:<br><br></p>

<ol>
   <li>How to be a good downstream - document decribing best practices about
handling Java</li>
   <li>Guide to using maven in ebuilds - this document is currently blocked by
not complete maven ebuilds (see 'Future plans')</li></ol><p><br></p>

 <p>We are also looking for articles that cover Java support on Gentoo.<br><br>
</p>

</li><li><p><b>QA / static analysis tools</b><br><br></p>

<p>Using die after eant in ebuilds has been deprecated: eant dies on it's own,
so:</p><pre>eant ... || die "eant failed"</pre>

<p>should be changed just to:</p><pre>eant ...</pre>

<p>New checks for usage of old eclasses and other QA issued are pending inclusion
in repoman. Additionaly pcheck will probably be implemented as repoman modules
after repoman gains ability to use such modules, not as separate tool. Eclipse
plugin for writing ebuilds (and performing QA checks) was started by zx, but is
currently unreleased. Gentoo user benny^work was also interested in such plugin,
but current state of this effort is unknown. Developer wltjr suggested making
such plugin also for Netbeans. One possible way of creating such plugin (aside
from implementing syntax highlighting) is to use Jython to use checks from
repoman.</p>
<p></p>

</li><li><p><b>Future plans</b></p>

<p>One controversial step, split of dev-java into more smaller categories, was
postponed for now. It will be reconsidered after stabilizing new Java build
system.</p>

<p>Additional third quiz will be required for new developers that want to join
Java team due to specifics of Java builds. Such quiz would have few general
questions about Java, handling classpath and ant builds.</p>

<p>State of maven on Gentoo was given by nelchael, and is available in attached
log. We are currently waiting for 2.0.5 release of maven to build it from source
and allow ebuilds to use it to build other software. For now it is required to
use `mvn ant:ant` to obtain build.xml file for ant, which can be used in
ebuilds.</p>

<p>Last two points of agenda: Java 1.6 and Java 1.7 were discussed. There was talk
about how to prepare for upcoming release of Java 1.6 in October (that date is
not fixed - it may change), troubles with JDBC drivers as Java 1.6 introduces
new functions, which breaks compilation of current JDBC3 drivers. Testing of
Java 1.7 has been postponed until 1.6 comes out.</p></li>

<li><p><b>Not on agenda</b></p>

<p>Following topics were mentioned that were not on agenda:</p>

 <ul>
  <li><p>Estimated EOL (end of life) for generation 1: until all ebuilds are<br>
migrated to generation 2 and stabilized we have to keep generation 1 alive in<br>
tree</p></li>
  <li>packages that break due to generation 2:
   <ul>
    <li><p>dev-java/jarjar</p></li>
    <li>dev-libs/cyrus-sasl</li>
    <li>kde-base/kdejava</li>
    <li>kde-base/qtjava</li>
    <li>app-accessibility/gnome-speech</li>
   <p></p></ul><br>
    That list is probably not complete.</li><br>
  <li>contacting David Herron (Sun employee, who helped to bring <a href="https://jdk-distros.dev.java.net/developer.html">DLJ licensed Sun JDK</a>
to potage) about packaging and distribution of other Sun Java packages, for
example <a href="http://java.sun.com/webservices/jwsdp/">Sun JWSDP</a>, JAF,
Javamail</li>
 </ul>
</li>
</ol>					