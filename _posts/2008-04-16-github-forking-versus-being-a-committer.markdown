--- 
permalink: /posts/github-forking-versus-being-a-committer
title: "GitHub: Forking versus being a committer"
tags: 
- git
layout: post
---
Git changes the game when it comes to how you collaborate. [GitHub](http://github.com) even more so.

If you want to collaborate with someone on GitHub, you have three options:

 * Add them as a committer
 * Have them fork your repository, and you pull changes as appropriate
 * Both

So which should you use? It depends.

If you will be working closely or in tandem with the individual, you might want to add them as a committer, allowing them to push to your repository at will. This is pretty comparable to how you might work with others on a subversion repo.

If it's more asynchronous work, forking would be more appropriate. This is particularly applicable to open source or large projects.

Of course, you could do both. They create a fork to use as their sandbox, and then you're able to push changes to your repository. I suspect that this is how the [rails repo](http://github.com/rails/rails/tree/master) works, with the [core team](http://github.com/rails/rails/wikis/the-core-team) having access to it, but having their own forks.


__Update:__

As the fates had it, a somewhat related post titled ['To fork or Branch'](http://toolmantim.com/article/2008/4/17/to_fork_or_branch) was posted over at [toolmantim.com](http://toolmantim.com/).