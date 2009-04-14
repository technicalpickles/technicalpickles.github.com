--- 
title: "Hacking on Wikipedia: links for 2008-03-20"
tags: 
- api
- links
- ruby
- wikipedia
layout: post
---
I was recently talking to a [friend](http://dancroak.com) about writing to screen-scraping tool for [wikipedia](http://wikipedia).

My first two thoughts:

 1. Scraping can be problematic at best
  * Dealing with nasty, nasty markup
  * Prone to change
 2. There has to be a better way
  * Preferably someone else has already done the legwork
  
After some digging, I uncovered the following bits:

 * [Wikipedia Database Download](http://en.wikipedia.org/wiki/Wikipedia:Database_download): You can get it as XML or SQL, with various degrees of details (ie all revisions, all languages, with comments, etc)
 * [MediaWiki API](http://www.mediawiki.org/wiki/API): Web service for various tasks (mostly just searching at the moment). Can get results in [many formats](http://www.mediawiki.org/wiki/API:Data_formats), including XML, YAML, and JSON
 * [Ruby client for Wikipedia API](http://code.google.com/p/wikipedia-client/): A Ruby wrapper in its infancy
 
The new API is very new, so your mileage may vary.

__Update:__

[Rob Cakebread](pythonhead@gentoo.org) pointed me at a couple more resources:

 * [DBpedia](http://dbpedia.org/About) a project to extract semantic information out of wikipedia and make it available on the web
 * [Linking Open Data dataset cloud](http://richard.cyganiak.de/2007/10/lod/) shows how DBpedia fits into the [Linking Open Data](http://esw.w3.org/topic/SweoIG/TaskForces/CommunityProjects/LinkingOpenData) project
