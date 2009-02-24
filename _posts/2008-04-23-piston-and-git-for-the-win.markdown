--- 
permalink: /posts/piston-and-git-for-the-win.html
title: piston and git for the win
tags: 
- git
- piston
- rails
- ruby
layout: post
---
[Piston](http://piston.rubyforge.org/) has had [support for git](http://blog.teksol.info/2008/3/17/piston-1-9-0-official-preview-release) (both importing from, and into) for a little over a month.

Before that, I felt like developing a rails app in git was a bit painful. Considering most plugins are kept in subversion, you're only real option is using `svn export` to install plugins, and check that into git. There was [braid](http://evil.che.lu/projects/braid), but I really didn't have any luck with it.

But, like I said, it's all good now. It hasn't been officially released (ie as gems), but you can build and install it yourself easily enough.

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">git clone git://github.com/francois/piston.git</kbd>
<samp class="prompt shell">$</samp> <kbd class="shell">sudo gem install -y main open4 log4r</kbd>
<samp class="prompt shell">$</samp> <kbd class="shell">cd piston</kbd>
<samp class="prompt shell">$</samp> <kbd class="shell">sudo rake install_gem</kbd></pre>

Pretty straight forward to use.

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">cd vendor/plugins</kbd>
<samp class="prompt shell">$</samp> <kbd class="shell">piston import</kbd> <samp>git://github.com/technoweenie/restful-authentication.git
INFO main: Guessing the repository type
INFO main: Guessing the working copy type
INFO main: Checking out the repository
INFO main: Copying from Piston::Revision(git://github.com/technoweenie/restful-authentication.git@42083ffa31e0a9792472780854ddd81bcc9b2f61)
INFO main: Checked out "git://github.com/technoweenie/restful-authentication.git" 42083ff to "restful-authentication"</samp></pre>

And since it's since it's being developed in a git repository, and on [GitHub](http://github.com), it's [extremely easy](http://technicalpickles.com/posts/github-requesting-your-changes-be-pulled-from-a-fork) [to start contributing](http://technicalpickles.com/posts/github-requesting-your-changes-be-pulled-from-a-fork).

I mean, if [I can contribute something](http://blog.teksol.info/2008/4/21/new-piston-contributor-josh-nichols), I'm sure you can pull it off too :)