--- 
title: Playing with heroku
tags: 
- git
- heroku
- rails
- ruby
layout: post
---
If you've been following things in the Ruby sphere, you might have heard of [heroku](http://heroku.com/). In my mind, it provides two major things:

 * Web UI for Rails development
 * Turnkey Rails hosting and deployment
 
I attended a workshop earlier today giving a shallow dive through Ruby and Rails, where we used heroku as our testbed.

### Initial setup

To start, you'll obviously need an account :) It is currently in invite-only beta, but I might be able to swing an invite or two, so drop me a note if you're interested.

Once you have an account setup, you should install their gem.

<pre class="terminal unix">
<samp class="prompt shell">urmachine $</samp> <kbd class="shell">sudo gem install heroku</kbd>
<samp>Updating metadata for 3 gems from http://gems.rubyforge.org/
...
complete
Successfully installed heroku-0.2
1 gem installed
Installing ri documentation for heroku-0.2...
Installing RDoc documentation for heroku-0.2...</samp>
</pre>

This gives you access to the basic stuff for managing your heroku apps.

### Creating an app

<pre class="terminal unix">
<samp class="prompt shell">urmachine $</samp> <kbd>heroku create urapp</kbd>
<samp>Enter your Heroku credentials.
Email: <kbd class="input">joshua.nichols@gmail.com</kbd>
Password:
Uploading ssh public key
Created http://<var>urapp</var>.heroku.com/</samp>
</pre>

This does a few things:

 * The first time you use any heroku commands, you will be prompted for your heroku email and password. It will also upload your public ssh key (which you need to create ahead of time) 
 * Creates a default app, as if you had done `rails urapp` locally
 * Sticks this in a remote git repository
 * Starts up the app, and makes it accessible at http://urapp.heroku.com
 
### Developing the app

At this point, you have two options for going forward with development.

 * Use the heroku web interface. Not for me.
 * Pull down a copy of your repository, work remotely and periodically push changes. Yes yes yes yes yes.

The `heroku` command line tool acts as a front to 

<pre class="terminal unix"><samp class="prompt shell">urmachine $</samp> <kbd>heroku clone urapp</kbd>
<samp>Initialized empty Git repository in /Users/nichoj/Projects/<var>urapp</var>/.git/
The authenticity of host 'heroku.com (67.202.2.198)' can't be established.
RSA key fingerprint is 8b:48:5e:67:0e:c9:16:47:32:f2:87:0c:1f:c8:60:ad.
Are you sure you want to continue connecting (yes/no)? <kbd class="input">yes</kbd>
Warning: Permanently added 'heroku.com,67.202.2.198' (RSA) to the list of known hosts.
remote: Generating pack...
remote: Done counting 56 objects.
remote: Deltifying 56 objects...
remote:  100% (56/56) done
Indexing 56 objects...
remote: Total 56 (delta 14), reused 0 (delta 0)
 100% (56/56) done
Resolving 14 deltas...
 100% (14/14) done
(in /Users/nichoj/Projects/<var>urapp</var>)</samp>
</pre>

You can now enter the `urapp` directory and hack away.

### Deploying

You'd think this be a long section but it isn't.

<pre class="terminal unix">
<samp class="prompt shell">urmachine $</samp> <kbd>git commit -a -m "behold my awesome changes."</kbd>
</pre>

This would just commit to your local repository though. Now push it.

<pre class="terminal unix">
<samp class="prompt shell">urmachine $</samp> <kbd>git push</kbd>
<samp>updating 'refs/heads/master'
  from 6ee014dfd32c5496b0cc4c3c7301e1c47b9f1b2f
  to   ed9a9e46ebeb90f1d288fc31063439938b9f7e00
 Also local refs/remotes/origin/master
Generating pack...
Done counting 44 objects.
Deltifying 44 objects...
 100% (44/44) done
Writing 44 objects...
 100% (44/44) done
Total 44 (delta 7), reused 0 (delta 0)
refs/heads/master: 6ee014dfd32c5496b0cc4c3c7301e1c47b9f1b2f -> ed9a9e46ebeb90f1d288fc31063439938b9f7e00
HEAD is now at ed9a9e4... behold my changes.
NOTICE:  CREATE TABLE will create implicit sequence "posts_id_seq" for serial column "posts.id"
NOTICE:  CREATE TABLE / PRIMARY KEY will create implicit index "posts_pkey" for table "posts"
NOTICE:  CREATE TABLE will create implicit sequence "comments_id_seq" for serial column "comments.id"
NOTICE:  CREATE TABLE / PRIMARY KEY will create implicit index "comments_pkey" for table "comments"
(in /mnt/home/userapps/18228)
== 1 CreatePosts: migrating ===================================================
-- create_table(:posts)
   -> 0.0361s
== 1 CreatePosts: migrated (0.0362s) ==========================================

== 2 CreateComments: migrating ================================================
-- create_table(:comments)
   -> 0.0367s
== 2 CreateComments: migrated (0.0368s) =======================================

Mongrel not running, can't restart
</samp></pre>

WHOA.

Not only did it push the commit, it seems to have some post-commit magic to migrate your database and restart mongrel.

### Impressions

Overall, this seems pretty slick. Lots of cool stuff going on.

 * Web UI for Rails development if you want it
 * git access for when you want to do local development
 * Effortless deployment
 * Really useful for trying out proof-of-concept apps
 * Free
 
The only concern I have is pricing. It's free now, but I can only imagine it'll be a pay service at one point or another. I'm told development will be free, but there will be some pricing scheme for making stuff publicly accessible.
