--- 
permalink: /posts/java-1-4-do-we-still-need-it.html
title: "Java 1.4: Do we still need it?"
tags: 
- gentoo
- java
layout: post
---

			<p>The answer, as of this moment, is most definitely. There are two reasons for this at the moment.</p>

<ol>
<li>there are still packages which don't compile when built with Java 1.5. In these cases, the new Java build system switches to a 1.4 JDK for building.</li>
<li>it is needed for packages that haven't been migrated to use the new Java system. This is necessary, otherwise we encounter the same exact problems that prompted the initial package.mask of Java 1.5.</li></ol>

<p>Just to recap why Java 1.5 was package was package.mask'd.... It really comes down to backwards compatibility, and that Java 1.5 isn't 100% backwards compatible with Java 1.4. Things that ran with Java 1.4 should still run with Java 1.5... but some things that once built with Java 1.4 no longer compile with Java 1.5. Specifically:</p>

<ol>
<li>There's a new reserved keyword with Java 1.5, enum. So where you used to be able to use enum as a variable name, you no longer can in 1.5.</li>
<li>A number of new methods have been introduced to some abstract classes and interfaces. If a particular implementation doesn't implement the new methods, then it won't compile against Java 1.5</li></ol>

<p>Now, say you're using Java 1.5 to build all your packages, and then you come up to a package that can't compile for one of these reasons? It'd fail for one. But then you may try with Java 1.4... and all is well until you see a compile error: UnsupportedClassVersionError. What does this mean though?</p>

<p>Well, each major release of Java has it's own version of bytecode. Bytecode is forward compatible, ie run 1.4 bytecode in 1.5, but not backwards compatible. If you try to use 1.5 bytecode in 1.4, you will see the infernal UnsupportedClassVersionError. This happens because, since you were using Java 1.5, the dependencies of the package you were just trying to compile with 1.4 were built with 1.5, and the default behavior is to build the highest possible bytecode.</p>

<p>So, these are the reasons Java 1.5 was package.mask'd, and why we need to use Java 1.4 for building unmigrated packages.</p>

<p>But how does the new Java system help?</p>

<ul>
<li>We get around the problem of enum by specifically telling the compiler to use source="1.4", where enum is a valid variable name.</li>
<li>We get around the changes in API by switching to a version where it can be compiled, until such time as the package can be properly patched.</li>
<li>Additionally, we make javac compile the lowest possible bytecode, so it can be used by as many other packages as possible.</li></ul>					