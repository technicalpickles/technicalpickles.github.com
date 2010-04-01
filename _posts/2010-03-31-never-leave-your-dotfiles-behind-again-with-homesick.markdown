---
layout: post
tags: dotfiles, vim, homesick
---

I was on a server the other day, and was getting a little weepy. I missed my dotfiles. The thought of doing this manually, especially on multiple servers, was even worse than the homesickness I had. Basically, I wanted:

 * A convention for laying out dotfiles repos
 * A command to clone my dotfiles from git
 * A command to symlink the files into my home directory
 * A delicious scotch

Lots of folks, myself included, have started sharing their configuration files, ie `.vimrc`, `.bashrc`, and `.screenrc`, on [GitHub](http://github.com) and elsewhere. Sometimes though, I think they are really more for personal backup and maybe to just share tips and tricks, rather than share them for consumption by others.

Why would I say such a thing? Well, let's say you found someone that has put together the ultimate set vim configuration. How would you go about using them?

 * `git clone whatever`
 * Peek into the repo, try to figure out how it's laid out and where the config files are
  * Are the files at the top level of the repo?
  * Do they include the dot in the file name, ie `.vimrc`, or exclude it so it's easier to see when doing `ls`, ie `vimrc`?
 * Symlink the files into your home directory


After deliberating and coding for an evening, I had something to show for my troubles: [homesick](http://github.com/technicalpickles/homesick). Basically, it provides what I just described, less the scotch.

    $ gem install homesick
    $ homesick clone git://github.com/technicalpickles/pickled-vim.git
       git clone  git://github.com/technicalpickles/pickled-vim.git to /Users/technicalpickles/.homesick/repos/pickled-vim
    $ homesick list
     pickled-vim  git://github.com/technicalpickles/pickled-vim.git
    $ homesick symlink pickled-vim
         symlink  /Users/technicalpickles/.homesick/repos/pickled-vim/home/.gvimrc to /Users/technicalpickles/.gvimrc
         symlink  /Users/technicalpickles/.homesick/repos/pickled-vim/home/.vim to /Users/technicalpickles/.vim
         symlink  /Users/technicalpickles/.homesick/repos/pickled-vim/home/.vimrc to /Users/technicalpickles/.vimrc
     

homesick assumes a few things about the repos it clones and how to symlink files from them:

 * It's a git repo (duh)
 * It contains a `home` directory
 * `home` contain any number of dotfiles, including the `.` prefix
 * Any file in `home` will be symlinked into the current user's home directory

homesick uses [thor](http://github.com/wycats/thor) under the hood, which provides a number of advantages:

* Pretty, pretty colors
* Simple parsing of commandline arguments
* Helper methods for creating files, directories, etc
* Builtin support for running the command without output, or only pretending to run the commands, or forcing the commands no matter what the cost
* Ease to add new 'commands', ie `symlink`, `clone`
* Conflict resolution when files already exist

Overall, I'm pretty happy where homesick is right now. It does what I wanted it to do, and looks pretty while doing so, so I can't really complain.
