--- 
title: "NFJS: Thoughts on \"OSGi: A Well Kept Secret\""
tags: 
- java
- nfjs
- osgi
layout: post
---
The next talk I attended was "OSGi: A Well Kept Secret" by [Venkat Subramaniam](http://agiledeveloper.com/blog).

I admit, I don't have first hand experience with OSGi. The closest thing would be that my IDE of choice, [Eclipse](http://www.eclipse.com), makes heavy use of it.

At the heart of it, OSGi is an means for managing modules, and their dependencies. What's a module, you ask? It's a just a jar. Yep, just a jar with a little metadata about itself, such as what modules it depends on.

So one way to think of OSGi is that it is a way to escape from classloader hell that some people fall into.

The most concrete example I can think of for this is [Spring](http://www.springframework.org) and [Hibernate](http://www.hibernate.org). Both use [asm](http://asm.objectweb.org/). Different versions. That aren't API compatible. So, depending on the whim of the classloader, one version may get loaded over the other, and if your unlucky, you'll get some fun runtime errors.

If these packages had been using OSGi, it would be a non-issue. It goes to great length to isolate modules from each other, and to ensure that a module will get class dependencies from the right module dependency.

At some point, one has to think... why wasn't this part of Java from the start?
