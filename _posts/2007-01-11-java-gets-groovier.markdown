--- 
title: Java gets Groovier
tags: 
- gentoo
- groovy
- java
layout: default
---

			<p>This news is a bit belated by a few days but.... over the weekend, I spent some time getting the recently released <a href="http://groovy.codehaus.org">Groovy 1.0</a> into the tree.</p>

<p>In case you hadn't heard about it, Groovy is, among other things, a dynamic language that runs on the the Java VM. Some interesting data points about it:</p>

<ul>
  <li>It's dynamic, for reals.</li>
  <li>It's about 98% syntax compatible with Java code (I made that number up, but really, it's pretty durn close).</li>
  <li>Java bytecode gets generated at runtime, no need to compile.</li>
  <li>Groovy can generate Java bytecode to your typical .class files</li></ul>

<p>A consequence of these points is that you can easily migrate from Java to Groovy, and have your project running exactly as it was. Then slowly, you can get into the swing of things, and take advantage of everything that Groovy has to offer as you get the hang of it.</p>

<p>One fear I've noticed of Java developers of jumping into a more dynamic language like python or ruby is late binding. You can't really be sure the type of your objects until runtime. Sure, you can read the documentation (assuming it's up to date) or peek at the code to be sure. With Java, binding occurs at compile time, so you can be sure that foo method expects a Bar, or that Bar has the aieeeee() method. I suspect some of this comes from defensive coding, where you become concerned about how your code will be (ab)used by others. </p>

<p>To combat this, a coworker and I have this idea... You use Java to define interfaces for the, uh, interfaces you care about. You can then use Groovy to implement these interfaces. By doing this, you get the best of both worlds: clients of your code get their strongly typed interface, and you get to do implementations with a more expressive syntax.</p>

<p>Overall, I've been pretty impressed with Groovy so far. Granted, I haven't used it as deeply as possible yet, but it certainly has a lot of potential.</p>

<p>In case any of this sounds interesting, here's some resources:</p>

<ul>
<li>The <a href="http://groovy.codehaus.org">Groovy homepage</a>, get your documentation on</li>
<li>The recently released <a href="http://www.amazon.com/gp/product/1932394842">Groovy in Action</a></li>
<li><a href="http://thediscoblog.com/">The Disco Blog</a>, by Andrew Glover, one of the authors of above mentioned book.</li></ul>

<p>And in other Java dynamicism news, I've added the freshly minted <a href="http://jruby.codehaus.org">JRuby 0.9.2</a> to the tree this past weekend as well. I haven't played with it yet, so I can't speak much on its behalf other than it's an implementation of Ruby written on the Java VM. Perhaps that can be the topic for a future blog post.</p>					