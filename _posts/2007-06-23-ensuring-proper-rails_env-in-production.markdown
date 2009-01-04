--- 
title: ensuring proper RAILS_ENV in production
tags: 
- gentoo
- rails
layout: default
---
When you're on your production machine, you probably want to always use the production environment. I've found myself often launching `script/console` only to accidentally start using it while in the development environment.

Here's one approach to ensuring this doesn't happen on Gentoo.

    technicalpickles ~ # echo "RAILS_ENV=production" >> /etc/env.d/99railsenv
    technicalpickles ~ # env-update
    >>> Regenerating /etc/ld.so.cache...
    technicalpickles ~ # source /etc/profile
    technicalpickles ~ # echo ${RAILS_ENV}
    production
    technicalpickles ~ # cd /u/apps/technical_pickles/current/
    technicalpickles current # script/console 
    Loading production environment.
    >> 

And there you go.

