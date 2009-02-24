--- 
permalink: /posts/configure-git-to-globally-ignore-some-files.html
title: Configure git to globally ignore some files
tags: 
- gentoo
- git
layout: post
---
If you think about it, there are always going to be some files you don't care about keeping under version control. Immediately come to my mind are:

 * .DS\_Store
 * vim swap files

Why should you always add these to your project's .gitignore? The answer is, you shouldn't, and you don't need to.

Try this. Toss the following in `~/.gitignore`:

<pre><code>.DS\_Store
*.sw?</code></pre>

Now run this:

<pre class="terminal unix"><samp class="prompt shell">$ </samp> <kbd class="shell">git config --global core.excludesfile ~/.gitignore</kbd></pre>

Good job! Now .DS\_Store and vim swap files will always be ignored.

Found this gem in the [PeepCode](http://www.peepcode.com) [Google Group](http://groups.google.com/group/peepcode/browse_thread/thread/fe6f9c1fc9d6e725).
