--- 
permalink: /posts/vimpocalypse.html
title: vimpocalypse
tags: 
- rails
- ruby
- vim
layout: post
---
Switching from TextMate to vim or emacs has been a trend on the rise. I'm riding the trend too, and it feels pretty good.

Coming from TextMate, there were a lot of things I missed. There were a lot of things I didn't even realize existed.

This post is meant to help folks who are trying to make the same switch. It isn't going to be the most extensive resource, but should point you in the right direction.

Oh, and just as a warning, one thing I've notice about reading vim resources is that they usually assume a certain level of familiarity with vim, and I don't think this will post will be any different. If something is over your head, well, that gives you a new area to learn about :)

### Resources

[A Byte of Vim](http://www.swaroopch.com/notes/Vim): This ebook starts at the very beginning, and walks you through pretty much every day-to-day type editing you're likely to encounter. I've been using vim for several years, but I must have been using it pretty poorly, because I learned a lot from it.

[Google](http://www.google.com): Seriously. Whenever there was something I didn't know how to do, googling for `vim <insert thing here>` worked nearly every time. Sometimes it took a few incantations.

[Vim Wikia](http://vim.wikia.com/): In said google queries, this wiki showed up about 50% of the time. Lots of great tutorials, walkthroughs, and tips.
 
[7 Habits For Effective Text Editing 2.0](http://video.google.com/videoplay?docid=2538831956647446078): [Bram Moolenaar](http://www.moolenaar.net/vim.html), one of the main authors of vim, gave this talk at Google. One of the takeaways I had from this is that it doesn't matter which editor you use, as long as you are using it effectively. If you do something that feels repetitive, stop, find a better way, and then start using it. In another way, constantly refactor how you use your editor to improve efficiency.

Friends, Coworkers, and Family: You probably know some people using vim. They make a good resource as well. I primarily tap them as a resource for how they are using a particular command/plugin, or how they are achieving a particular goal. Be sure to do some research first, or you might get hit with a [letmegooglethatforyou](http://letmegooglethatforyou.com/).
 
### Plugins

vim is amazingly extensible. There are also a lot of clever folks across the tubes. As a result of this, there are a lot of great plugins out there to assist your editing.

These are the plugins I'm using, and I'll include any tweaks or mappings I'm using. These would go in your `~/.vimrc`

[rails.vim](http://www.vim.org/scripts/script.php?script_id=1567): For me, it's all about being able to navigate around your project. For example, while editing a controller, you can easily switch to the functional test, or from a model to the unit test, and vice versa (`:A`). If you have your cursor on a `render :partial`, you can easily navigate to the partial (`gf`). This is only a sampling of what it can do though.

[NERD Commenter](http://www.vim.org/scripts/script.php?script_id=1218): Lets you easily comment out code. I made command-/ comment out code, kinda like in textmate.

    " bind command-/ to toggle comment
    " requires NERD Commenter to be installed: http://www.vim.org/scripts/script.php?script_id=1218
    nmap <D-/> ,c<space>
    vmap <D-/> ,c<space>
    imap <D-/> <C-O>,c<space>

[NERD Tree](http://www.vim.org/scripts/script.php?script_id=1658): File navigation using a directory tree. I setup \d to toggle it being visible.

    " bind \d to toggle file browser
    " requires NERDTree
    nmap <leader>d :NERDTreeToggle<CR>
    
[autoclose.vim](http://www.vim.org/scripts/download_script.php?src_id=7700): This will automatically close things like quotes, brackets, parantheses, etc. I've found it to be a little buggy at times, so I'm considering dropping it, or finding something else. YMMV.
 
[bufexplorer.vim](http://www.vim.org/scripts/script.php?script_id=42): vim keeps track of all the files you've edited, and has ways of navigating between them (`:bp` and `:bn` for example). bufexplorer gives you a much more visual way of do this. It displays a window with all the files you've edited, you navigate to the file you want, and hit enter, and whoops, editing the file now. By default, it binds to \be.

[endwise.vim](http://www.vim.org/scripts/script.php?script_id=2386): Kind of similar to autoclose, but more for structures you'd encounter while programming, such as if statements and class declarations.

[fuzzyfinder\_textmate](http://github.com/jamis/fuzzyfinder_textmate/tree/master): This is the texmate equivalent of 'Go to File'. Pull it up, and start typing a filename. I bind it to \t (command-t opens a tab in MacVim).

    " binds \ t to textmate-style fuzzy finder
    map <leader>t :FuzzyFinderTextMate<CR>
    let g:fuzzy_matching_limit=60 " this seems to help performance
    let g:fuzzy_ceiling=20000     " I have some projects with a lot of files...
    
[allml](http://www.vim.org/scripts/script.php?script_id=1896): Gives you some ways to interact with the various HTML-like code that various programming languages / frameworks provide, like shortcuts to create `<% %>` in ERB

[matchit](http://www.vim.org/scripts/script.php?script_id=39): vim has builtin support for 'bouncing' between parentheses using the % key. matchit extends it further to work with things like if blocks, class declarations, etc.

[gist.vim](http://www.vim.org/scripts/script.php?script_id=2423): Post stuff to [Gist](http://gist.github.com).

[snippetsEmu](http://www.vim.org/scripts/script.php?script_id=1318): Emulates textmate snippets. I've found I don't actually use this as much as I would have thought.

[surround.vim](http://www.vim.org/scripts/script.php?script_id=1697): Tools for 'surrounding' your text. For example, add quotations, switch it to paraentheses, etc.

[taglist.vim](http://www.vim.org/scripts/script.php?script_id=273): vim has [support for browsing by 'tags'](http://vim.wikia.com/wiki/Browsing_programs_with_tags), which is to say, by method name, class name, etc. taglist gives you a way of seeing tags in the current file. It brings up a new window, which you can browse to the tag you want, and then automatically navigate to it. I've tried to make it act like 'Go To Symbol' in textmate. \T toggles the taglist, it gets focus, and then goes away after you navigate to something.

    " binds \ T to taglist (sorta like textmate apple-shift-t)
    map <leader>T :TlistToggle<CR>
    let Tlist_Show_Menu=1
    let Tlist_GainFocus_On_ToggleOpen=1
    let Tlist_Close_OnSelect=1
    let Tlist_Compact_Format=1
 
[ack](http://blog.ant0ine.com/2007/03/ack_and_vim_integration.html): Unfortunately not a packaged plugin, but you can just drop the code snippet (the second, updated one) in ~/.vim/plugin/ack.vim. This lets you search for stuff in your current directory, and gives you a way to browse the results. Kinda like textmate's 'Find in Project'. I made \F start the command off for you.

    " \F to startup an ack search
    map <leader>F :Ack<space>
 
### Sagelike advice

These are bits and pieces I've picked up along the way.

#### TextMate-like functionality

Not textmate specific, but you can map stuff to the command key in MacVim using M. For example, `<M-/>` would be command-/

If you have problems with fuzzfinder\_textmate being able to find the Ruby classes, I found just placing `fuzzy_file_finder.rb` in `~/.vim/ruby` fixed the problem.

Insert hashrockets (`=>`) while in insert-mode, just like in TextMate:

    " bind control-l to hashrocket
    imap <C-l> <Space>=><Space>

Indent and unident with `command-[` and `command-]`:

    " bind command-] to shift right
    nmap <D-]> >>
    vmap <D-]> >>
    imap <D-]> <C-O>>>

    " bind command-[ to shift left
    nmap <D-[> <<
    vmap <D-[> <<
    imap <D-[> <C-O><<
    
Switch to different tabs using command-1 through command-9:

    " open tabs with command-<tab number>
    map <D-1> :tabn 1<CR>
    map <D-2> :tabn 2<CR>
    map <D-3> :tabn 3<CR>
    map <D-4> :tabn 4<CR>
    map <D-5> :tabn 5<CR>
    map <D-6> :tabn 6<CR>
    map <D-7> :tabn 7<CR>
    map <D-8> :tabn 8<CR>
    map <D-9> :tabn 9<CR>

#### Simplify window navigation

vim supports splitting a window vertically and horizontally, and then navigating between them. The default way kinda hurts my hands. Let's reduce it to \s to split horizontally, \v to split vertically, and \w to cycle to the next window.

    " window splitting mappings
    " split vertically with <leader> v
    " split horizontally with <leader> s
    nmap <leader>v :vsplit<CR> <C-w><C-w>
    nmap <leader>s :split<CR> <C-w><C-w>
    
    " Make it way easier to switch windows (<leader>w)
    nmap <leader>w <C-w><C-w>_
    
Gleefully stolen from [James Golick](http://jamesgolick.com/)'s [dotfiles](http://github.com/giraffesoft/dotfiles/tree/master).
    
#### Other stuff, with less explanation

    set nocompatible          " We're running Vim, not Vi!
    syntax on                 " Enable syntax highlighting
    filetype plugin indent on " Enable filetype-specific indenting and plugins

    set incsearch             " Incremental searching
    set hlsearch              " Highlight search results once found:
                              " http://vim.wikia.com/wiki/Highlight_all_search_pattern_matches
    set number                " can has line numbers?
    set cursorline            " highlight the current line the cursor is on
    set showmatch             "sm:    flashes matching brackets or parentheses
    set smarttab              "sta:   helps with backspacing because of expandtab

### Fin

Whew. That was a lot longer than I had anticipated. Be sure to leave a comment if you have a question, or have better ways of doing any of this.
