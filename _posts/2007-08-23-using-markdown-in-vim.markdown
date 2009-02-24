--- 
permalink: /posts/using-markdown-in-vim
title: Using Markdown in vim
tags: 
- bluecloth
- markdown
- rails
- vim
layout: post
---
My posts are written in [Markdown](http://daringfireball.net/projects/markdown/), by using the wonderful [BlueCloth](http://www.deveiate.org/projects/BlueCloth) library, as I discussed in [this earlier post](/blog/permalink/switched-templating-engine-to-bluecloth.html).

Considering this is my first Rails project, it definitely lacks a certain... sophistication. For example, posts are either published and live, or don't exist yet. And also, I don't have any autosave mechanism.

These two areas of want combined to make me a very sad panda one day. After writing up the post of the day, I may have restarted my computer, forgetting that I had a lovely work of writing in progress. Whoops. Got a little upset by that.

Until I have time to implement those two features, I found a suitable stop-gap: vim! So I've taken to simply writing stuff up in vim, and when it's ready, copy, paste, and publish. Easy peasy.

But where would vim be without some syntax highlighting? NOWHERE! Well, it's still pretty awesome regardless, but I don't think many would disagree that syntax highlighting is a bad thing.

While there isn't support out of box, some [clever fellow](http://www.plasticboy.com/) wrote something up to do [the trick](http://www.plasticboy.com/markdown-vim-mode/). While I'm sure you could easily browse there to see how to do this, I'll reproduce it here as well.

Download [this](http://www.plasticboy.com/dox/mkd.vim), and place it in ~/.vim/syntax/ (of course, creating it if it doesn't exist).

Now create, or add to ~/.vim/filetype.vim:

    " markdown filetype file
    if exists("did\_load\_filetypes")
     finish
    endif
    augroup markdown
     au! BufRead,BufNewFile *.mkd   setfiletype mkd
    augroup END

And then to ~/.vimrc and/or ~/.gvimrc

     augroup mkd
      autocmd BufRead *.mkd  set ai formatoptions=tcroqn2 comments=n:>
     augroup END

I kind of feel like a plugin, or something, could take care of this for you, but I don't know enough vim voodoo to sure.

Now you have vim markdown goodness... Enjoy!
