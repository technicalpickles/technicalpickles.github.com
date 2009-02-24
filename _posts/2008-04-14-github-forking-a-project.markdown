--- 
permalink: /posts/github-forking-a-project
title: "GitHub: Forking a project"
tags: 
- git
- ruby
- shoulda
layout: post
---
If you hadn't heard, [GitHub](http://github.com) went [officially live](http://github.com/blog/40-we-launched) last week.

As [I've](/posts/git-links-for-2007-01-05) [posted](/posts/gitosis-on-gentoo) [before](/posts/more-git-links-2008-02-14), git is pretty awesome. GitHub makes it even better.

In honor of the launch, here's a quick rundown of forking a repository to start hacking away.

First off, you need to find a victim. In this case, I'm going to go after [thoughtbot](http://thoughtbot.com/)'s [Shoulda](http://thoughtbot.com/projects/shoulda).

First navigate to the [repository](http://github.com/thoughtbot/shoulda/tree/master), and click 'fork'.

<div><img src="http://img.skitch.com/20080414-cbdem2eer6kr2mtuubuq9msc9c.jpg"/></div>

Give it minute while the hardcore forking action happens. Now you have your own fork of the repository.

<div><img src="http://img.skitch.com/20080414-prbh2q19n4gef8fa86m4ycxw6s.jpg"/></div>

Here you can see your clone/push URL. So we should go ahead and clone it:

    git clone git@github.com:technicalpickles/shoulda.git
    
So we have a checkout now. As is, it's kind of detached from the official repository. If we're going to be actively working on it, we should add the official repository as a remote.

    cd shoulda
    git remote add official git://github.com/thoughtbot/shoulda.git
    
From now on, we should be able easily pull in the changes:

    git pull official master
    
So now what? Get hacking of course. But how do send our changes back? Well, this is a tricky question. There are several ways, but it mostly depend on those receiving the changes back. As people get more comfortable with git, I think we'll start to see some best practices emerge here.