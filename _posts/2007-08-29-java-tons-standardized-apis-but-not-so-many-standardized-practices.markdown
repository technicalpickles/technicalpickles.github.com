--- 
permalink: /posts/java-tons-standardized-apis-but-not-so-many-standardized-practices.html
title: "Java: Tons Standardized APIs, but not so many standardized practices"
tags: 
- gentoo
- java
- practices
- tools
layout: post
---
Java has more standardized APIs than you can shake a stick at. Servlets, JSP, EJB, JPA, and the number is always growing as people put together new JSRs. There's even more defacto standards, like [Spring](http://springframework.org/), [Ant](http://ant.apache.org/), [Maven](http://maven.apache.org/), etc.

What you don't see is standardized practices for common things you have to do when you maintain an application. I'm not talking about code here. I'm talking about things like getting a development environment going, or maintenance of configuration and database schema, or deploying applications to staging areas or production.

Time and time again, it seems like different companies / developers / etc roll their own ad hoc solutions to these problems.

I'll illustrate with the example of building a developer environment.

## Developer environment

Setting up developer environments typically sucks. Does this sound familar?

  * Download a JDK, install it somewhere
  * Download [Tomcat](http://tomcat.apache.org/), extract it somewhere else
  * Download [Eclipse](http://www.eclipse.org/), extract it somewhere random
  * Download [Ant](http://ant.apache.org), extract it somewhere completely different
  * Hack together a .bash_profile, and update the PATH to point at all these random paths you've put together

If you are lucky, there might be scripts to automate this, or maybe a tarball with everything you need and the appropriate shell files to get a sane shell environment.

If you are even luckier, you are running [Java on Gentoo](http://java.gentoo.org), and can just do 'emerge www-servers/tomcat dev-util/eclipse-sdk dev-java/ant'.

## How can we right this wrong?

So what made building a dev environment less sucky? Tools.

In the first scenario, there were none. You were left to manually do everything yourself. Need to upgrade something? Have fun figuring out where you should drop the files and ensuring that your shell scripts are pointing at the right locations. Accidentally threw your machine out the window and have to build a new one from scratch? Good luck remembering all the intricacate steps you took to get to your last good state. The result: Pain to reproduce, and annoying to maintain.


In the next scenario, you do have some tools for getting things an initial environment going. But there probably aren't tools for making sure you're up to date. So while you can reproduce environments readily enough, you can't necessarily maintain them easily over time, and now you have the additional burden of maintaining these tools.

In the last scenario, we're entirely leveraging tools that are already out there. But what if it doesn't quite fit your needs? Well, it's all open source, so you have all the access you need to improve things. And for bonus points, you can improve things in a general enough way so that you can send your improvements back upstream so that everyone using the tools can benefit.

So what exactly are the tools doing here?

  * Making your life easier
  * Creating de facto standards

## Summary

Build a better future through superior tools. Yeah, I like the ring of that.

