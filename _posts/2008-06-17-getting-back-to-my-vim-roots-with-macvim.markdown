--- 
title: Getting back to my vim roots with MacVim
tags: 
- macvim
- rails
- ruby
- vim
layout: post
---
Back when I got my MacBook last fall, I chose to go with [TextMate](http://macromates.com/) for Ruby and [Rails](http://www.rubyonrails.org) development, despite being a long vim user.

And it was good. I think it was deserving of the hype it usually receives from Ruby developers on Macs. Except for the paying for software thing... I still feel dirty about that.

Recently [a friend](http://dancroak.com) ditched TextMate cold turkey in favor of vim, after seeing the cool and useful stuff [coworkers](http://www.thoughtbot.com/about/people) could do with it. After seeing it myself, let's just say I'm going back to my roots.

If you're on a Mac, you just _have_ to use [MacVim](http://code.google.com/p/macvim/) (not to be confused with [this MacVim](http://macvim.org/)). It's basically like gvim, except for Mac OS. Duh.

There are a lot really small, nice integration points and features I've been noticing.

 * Mac-like text navigation
  * option-left/right for navigating words
  * apple-left/right for start/end of line
  * apple-up/down for start/end of document
 * Shortcuts using the apple key
  * apple-s for saving
  * apple-n for new item
  * apple-a for select all. ggVG always felt annoying for doing this
 * Recent documents
 * Tabbing (apple-t creates a one, apple-w closes it)
 * Command line tool, `mvim` (similar to the `mate` command). It seems to aaccept the same options you'd expect of vim and gvim.

Similar to gvim using `~/.gvimrc`, MacVim uses `~/.macvimrc`. Personally, I've taken to putting everything in `~/.vimrc`, and just creating a symlink.

I'm still working on replicating most of the functionality I came to find useful for Ruby development in TextMate, but I will keep you posted.

I'm also vaguely considering doing some screencasts with vim tips/tricks. One thing I'd want though, is a good way to show what keys are being pressed. I remember back in OS9 days, there was a utility for doing this, but I haven't found a modern equivilant. 
