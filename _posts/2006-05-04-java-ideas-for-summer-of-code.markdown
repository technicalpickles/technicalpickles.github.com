--- 
permalink: /posts/java-ideas-for-summer-of-code
title: Java ideas for Summer of Code
tags: 
- gentoo
- google
- java
layout: post
---

			<p>As has been previously announced, we are officially part of Google's Summer of Code. I've decided to step up to be a mentor, and in particular, to mentor projects related to Java on Linux.</p>

<p>A few ideas follow. The 1a and 1b are listed already on our SoC page to an extent, but the idea is elaborated further here. If you plan on proposing one of these ideas, I really would expect you to even further elaborate.</p>

<p><strong>1a)</strong><br>
<em>Summary:</em><br>
   </p><p>Build most of our Java packages with free (libre) virtual machines and free implementations of public APIs.</p>

<p><em>Background:</em><br>
  </p><p>Currently, we really only support using a proprietary virtual machine (ie sun, blackdown, ibm, etc), because packages are likely to fail for various reason with the open ones.</p>
   <p>For many open apis, such as javamail, java activation framework, etc, we have binary packages of Sun's proprietary implementations. In a number of cases, there are open implementations. However, our packages compile against and run using the proprietary implementations.</p>
   <p>For reasons why one would want to be using Free Java, see the article on <a href="http://www.gnu.org/philosophy/java-trap.html">the Java trap</a>.</p>
   <p>As for a practical reason, use of proprietary packages from Sun and IBM can annoying for the end user, because in both cases, it requires placing a fetch restriction on the distfiles. To the end user, this means that an emerge gets halted until they agree to a license, and download the files.</p>

<p><em>Goals:</em>
</p><ul>
   <li>Build/run all/most packages using free virtual machines</li>
   <li>Build/run all/most packages against free implementations of public APIs</li>
   <li>Might want to target specific big name packages, like eclipse, azureus, tomcat.</li>
   <li>Be able to select between different implementations of the same apis</li></ul>

<p><em>Tasks:</em>
</p><ul>
   <li>Work with upstream of packages that use propertary classes from the virtual machine (ie com.sun.*, sun.*)</li>
   <li>Work with upstream of virtual machines and packages when packages don't compile or run with using free java</li>
   <li>Find and package open implementations of public APIs</li></ul>

<p><em>Hurdles:</em>
</p><ul>
   <li>Lots of Java packages (300+). It is unknown how many will need to be patched.</li>
   <li>Upstream might not care about free java</li>
   <li>Might not be open implementations of all APIs</li></ul>

<p><strong>1b)</strong></p>

<p><em>Summary:</em><br>
   Native compiling with gcj</p>

<p><em>Background:</em><br>
   </p><p>GNU Compiler for Java (gcj) allows for compiling Java code to native machine code, as opposed to Java bytecode. Fedora/Redhat has done this to an extent for some time. It'd be great to be able to do this under Gentoo as well.</p>

<p><em>Tasks:</em>
</p><ul>
   <li>Create a 'JDK' which uses gcj as a backend. In this case, JDK really means providing a javac, javadoc, jar, etc</li>
   <li>Have a way of keeping track of native bytecode</li>
   <li>Integrate methods for native compiling into the current build system for building Java packages on Gentoo (ie eclasses)</li>
   <li>Target native compilation of high profile applications, ie azreus, eclipse, freemind</li></ul>

<p><strong>2a)</strong></p>

<p><em>Summary:</em><br>
   </p><p>Build <a href="http://maven.apache.org">maven</a> entirely from source.</p>

<p><em>Background:</em><br>
   </p><p>We currently only provide binary packages of maven, due to the fact that it is a bootstrapping nightmare because of the monolithic nature of the build process. The build system, at its basic level, does the following:</p>
<ul>
   <li>Build the 'core' maven functionailty using ant</li>
   <li>Build the 'core' maven functionality using maven</li>
   <li>Build all the plugins using maven</li></ul>

   <p>The problem with building all the plugins at once is that there are a ton of plugins, with many, many dependencies. Because of these dependencies, it would make it impractical to have a monolithic package, which would be used to build other packages.</p>

<p><em>Goals:</em>
</p><ul>
   <li>Have modular packages for maven</li>
   <li>Have one package per plugin</li>
   <li>Have a 'core' maven package</li>
   <li>Have a 'minimal' maven package, which is core + minimal packages for compiling, javadocs, and jarring</li>
   <li>Have a 'full' maven package which is all the plugins</li>
   <li>Would need to work out a way to make the build much more modular</li></ul>

<p><strong>2b)</strong></p>

<p><em>Summary</em><br>
   Build packages using maven</p>

<p><em>Background</em><br>
   Many packages, particular Apache projects, have been switching over</p>

   <p>The main issue is getting jars from a maven repository. The normal behavior of maven is to download dependencies from a maven mirror. First<br>
of all, things at build time should not be using the network. Secondly, dependencies should be provided by jars from packages that have previously been emerged.</p>

   <p>Another issue is that maven 2 repositories contain metadata (specifically, pom.xml files) about the packages contained within. This is normally fetched from the repository, but since we don't want to be pulling from there, the question remains open as to where the metadata is gotten from and how its stored on the system.</p>

<p><em>Goals:</em>
</p><ul>
   <li>Have a system to have maven use jars that we have built through portage</li>
   <li>Update packages to use maven when possible. These should have fine grain control to the plugins that are available at build time. Packages should depend on the minimal install, and whatever extra plugins they might need, and only those should be available at build time.</li></ul>
<p><em>Possible solutions for dealing with maven repositories:</em><br>
       A) Create a maven-repository like structure, and have it populated with jars from the system<br>
       B) maven (2.0 at least) has a mechanism for providing alternative ways of fetching dependencies. We could then create an alternative way that's backed by portage.<br>
       C) Find a way to tell maven not to fetch dependencies, and hope that there are the appropriate classes  available on the classpath<em></em></p>					