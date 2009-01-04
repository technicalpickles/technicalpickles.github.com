--- 
title: "GitHub: Requesting your changes be pulled from a fork"
tags: 
- git
- ruby
- shoulda
layout: default
---
After [forking](/posts/github-forking-a-project) the recently gitified [Shoulda](http://thoughtbot.com/projects/shoulda), I started poking around. First thing I noticed is tests were failing because some directories were missing. Presumably, this is because git ignores empty directories.

Let's use this opportunity to using git and [GitHub](http://github.com) to contribute a fix for this, shall we?

Here's a sky high view of the process:
 
 * Create a fix branch
 * Make changes
 * Push fix branch to GitHub
 * Browse to branch in browser
 * Submit a 'pull request' to the maintainer for review
 
After the maintainer gets the request, they are free to do as they see fit. Accept it, tweak it, kick it, and so on.
 
## Fixing it

First thing's first. We should create a branch off of master to isolate the fix:

    git checkout -b add_missing_dirs

Now the tricky part: actually fixing it. When everything is ready, I just commit it:

    git commit

As always, it's good to include meaningful commit messages. Keep in mind that if accepted, this message will become eternally bound to the repository.

With that out of the way, we actually need to push it to our 'origin' remote. This would be GitHub.

    git push origin  add_missing_dirs

## Requesting the pull

We can now go to [our repository on GitHub](http://github.com/thoughtbot/shoulda/tree/master). We want to browse to the `add_missing_dirs` branch. Mouse over 'all branches' to get there.

<div><img src="http://img.skitch.com/20080414-mq5xnqepqw66tadpjimdqj2ntu.jpg"/></div>

This gets us to the [branch](http://github.com/technicalpickles/shoulda/commits/add_missing_dirs). Next, we indicate our desire to submit a pull request.

<div><img src="http://img.skitch.com/20080414-gysdr3u76tnfuy4bgw27rpdb7d.jpg"/></div>

This prompts us for some details. I only had one commit, so I used its log as the message. I added [tsaleh](http://github.com/tsaleh) aka [Tammer Saleh](http://tammersaleh.com/) as the recipient, since he's the maintainer of Shoulda.

<div><img src="http://img.skitch.com/20080414-fk7kre51hk6wfr3xw41nbwp5mi.jpg"/></div>

After you submit, you'll get a notice that it was sent successfully, and you'll be returned to your branch. The recipients now get a notice about this pull request, and can take appropriate action from there.

## Done and done

Congratulations! You are now at the mercy of the maintainer.