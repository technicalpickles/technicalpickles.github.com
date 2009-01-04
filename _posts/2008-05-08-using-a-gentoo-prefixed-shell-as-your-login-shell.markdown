--- 
title: Using a Gentoo Prefixed shell as your login shell
tags: 
- gentoo
- mac
- prefix
layout: default
---
One thing that kind of annoyed me about Gentoo Prefix is that you always needed to do a little work to enter the prefix:

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">./bootstrap-prefix.sh /path/to/prefix startscript</kbd></pre>
    
Personally, I just want to always live in a prefixed shell. Amazingly enough, this is really easy to do.

 1. First open up `/etc/shells` as root
 2. Add a line like `/path/to/prefix/bin/bash` and save
 3. As your user, run: `chsh -s /path/to/prefix/bin/bash`
 4. Enter your password
 5. Done

Now, everytime you open a terminal, it will be running a bash gloriously built by Gentoo