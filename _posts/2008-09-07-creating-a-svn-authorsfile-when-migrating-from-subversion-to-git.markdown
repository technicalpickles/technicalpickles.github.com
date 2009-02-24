--- 
permalink: /posts/creating-a-svn-authorsfile-when-migrating-from-subversion-to-git.html
title: Creating a svn.authorsfile when migrating from subversion to git
tags: 
- git
- subversion
- svn
layout: post
---
There are plenty of tutorials already out there on how to migrate a subversion repository to git. This is the first one I found, and it seems pretty good: [Cleanly Migrate Your Subversion Repository To a GIT Repository](http://www.simplisticcomplexity.com/2008/03/05/cleanly-migrate-your-subversion-repository-to-a-git-repository/)

All the tutorials I found generally tell you to write the svn.authorsfile by hand. This is easy enough to do, if there are only a handful of authors, and you know exactly who they are.

The kink is if you forget an author, git-svn bails out when it sees an author that wasn't in the svn.authorsfile. You have to tweak the file,  and start over, hoping that you got them all.

This can be a fairly time consuming process, considering that a repo with some 500 commits took about 15 minutes to migrate.

Wouldn't it be nice if there was a way to automatically generate a svn.authorsfile based on the subversion history?

I thought so, so I wrote this up:

    #!/usr/bin/env bash
    authors=$(svn log -q | grep -e '^r' | awk 'BEGIN { FS = "|" } ; { print $2 }' | sort | uniq)
    for author in ${authors}; do
      echo "${author} = NAME <USER@DOMAIN>";
    done

Run this inside an subversion checkout. It outputs a template for the svn.authorsfile to the console, so you just need to paste it into a document, and fill in the names and email address for your authors.