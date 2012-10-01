--- 
title: Dealing with libraries that like breaking their APIs
tags: 
- groovy
- hibernate
- java
layout: post
---

<p>I'm currently working on making use of a <a href="http://groovy.codehaus.org/">Groovy</a> on a project of mine which uses <a href="http://www.hibernate.org">Hibernate</a> and <a href="http://www.springframework.org">Spring</a> among other things.</p>

<p>My code wasn't particularly well tested in terms of unit tests. By this, of course, I mean there were no unit tests.</p>

<p>So like a good code monkey, I decided to get some unit tests going, verify they work, then move code over to use Groovy. The benefit here being that by having these tests, I can verify that things still work as expected after the migration.</p>

<p>I figured on trying out <a href="http://rmock.sourceforge.net">rMock</a>, one of the many mocking libraries out there for Java. I get my first test case written, cross my fingers, and hit the run button (this is in Eclipse)... and what do I get, but the infamous red bar. NoClassDefFound... WTFBBQ?!? For whatever reason, it wasn't finding certain classes from dev-java/asm.</p>

<p>I won't go into further detail about how I found this problem, and found the cause, but here are my findings (versions here refer to slots):</p>

<ul>
<li>dev-java/groovy uses dev-java/asm-2.2</li>
<li>dev-java/hibernate uses dev-java/cglib-2.1</li>
<li>dev-java/rmock uses dev-java/cglib-2.1</li>
<li>dev-java/cglib-2.1 uses dev-java/asm-1.5</li>
</ul>

<p>The issue here is that asm apparently changed it's API quite a bit between 1.x and 2.x. This problem seems to have cropped up a few times at least according to google (search for 'NoClassDefFoundError: org/objectweb/asm/CodeVisitor').</p>

<p>The asm tutorial even goes so far as to the changes... kind of. See <a href="http://asm.objectweb.org/doc/tutorial-asm-2.0.html">here</a> under the "Changes in the ASM 2.0 API Since ASM 1.x" section. A few things bother me about this.
</p>
<ul>
<li>I don't see why the major changes, like renamed classes, and changed method signatures, couldn't have been deprecated at first, and do some twiddling to proxy calls to deprecated things to the newer ones.</li>
<li>The line that goes like "In general, it would be a good idea to run tool like JDiff and review the differences between the ASM 1.x and 2.0 APIs." Maybe it's just me, but this seems a bit presumptious. I mean, while such a tool is probably useful, it is far from thorough. It might point out where the API changed, but would have no clue as to the significance of those changes, or how you would account for them.</li>
</ul>

<p>A question that might come out is why would rmock expose this problem, when hibernate is using the same version of asm? Presumably it is because hibernate isn't exercising the parts of the cglib API that use the offending parts of asm, so I must have lucked out before now.</p>

<p>Regretfully, this seems somewhat common in the Java world. Maybe not very common, or even common, but common enough that they stick out in my mind, causing a handful of angst and frustration.</p>

<p>There is some good news though. cglib-2.2_beta1 seems to have been updated to use the asm-2.x's API. Another bit of fortune is that it's unlikely that anything in the tree will run into this particular snaggle, just code monkeys like me using these various libraries.</p>

<p>For the record, I don't get the ClassDefNotFound anymore, but instead I get a lovely NullPointerException from down in Groovy. Oh well, one step forward, one step back.</p>					
