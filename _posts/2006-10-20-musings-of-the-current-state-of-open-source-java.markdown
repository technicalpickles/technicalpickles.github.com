--- 
permalink: /posts/musings-of-the-current-state-of-open-source-java.html
title: Musings of the current state of open source Java
tags: 
- gentoo
- java
layout: post
---

			<p>[Note: I'm actually referring to open source Java software and libraries, not Java itself]</p>

<p>I've been maintaining and working with Java packages for pretty close to a year and a half now. And I hate to say it, but it is only becoming more apparent that things are less than ideal.</p>

<p>Sure, there are a multitude of projects out there, great projects. Eclipse, Tomcat, Netbeans, Azureus, all of the Apache Jakarta project, and many more. For a developer, it's great.</p>

<p>For a packager... it's not so nice of a picture. Builds packages vary from project to project with some kind of unholy glow. While there are some defactor standards out ther e, ie using ant or maven, it ain't pretty.</p>

<p>How do you build your javadocs? Is the target named doc, docs, javadocs, javadoc, or api?<br>
How do you manage your dependencies on third party libraries? Include every jar under the sun, bundle the source in with your own, or download the from the interweb?</p>

<p>The latter of these is particularly irksome. As I said, the standard practice is generally to include all your dependencies. I recall reading an O'Reilly book on ant at some point, and they actually encourage this behavior. I seem to think they said something along the lines of "You can't trust your user to use the right dependencies, so you should include them so that things will definitely work."</p>

<p>I'm fairly sure most of this be traced back to influences of the Windows world. I mean, there really isn't a concept of dependencies on other packages. You just distribute your application in a giant blob, and be done with it.</p>

<p>While I'm sure it's convenient if your developing the application or distributing a single blob to a user, it certainly is a pain to package.</p>

<p>So for all the dependencies, we want to package the dependencies, and then package the dependencies' dependencies, and so on. Then we want to make the application use our copy of the dependency that we just packaged. Easy enough, right?</p>

<p>Not quite. Here are a few of the many problems we come up against all too regularly:
</p><ol>
  <li>The API of the dependencies are unstable, as in, they break between minor revisions.</li>
  <li>The dependency has been patched in fun and exciting ways that are specific to the application.</li>
  <li>The origin of the dependency is unknown, and no upstream can be found.</li></ol>

<p>Quite dreary, eh?</p>

<p>Fortunately, it is not always this bad. There are, of course, good applications and libraries which are sane enough to package. Maven, in some senses, has been helping improve things, by standardizing the way things are being built, and by providing a standard mechanism for getting dependencies. A step in the right direction, at the very least.</p>

<p>Stay tuned next time for some ideas for addressing these concerns...</p>					