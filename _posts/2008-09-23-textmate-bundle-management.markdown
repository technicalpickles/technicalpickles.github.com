--- 
title: TextMate Bundle Management
tags: 
- haml
- html
- nginx
- rails
- ruby
- shoulda
- textmate
layout: post
---
Installing and managing [TextMate](http://macromates.com/) bundles has always been a pain. You have to check it out from source, figure out where in the filesystem it goes, and restart TextMate.

Fortunately, [someone](http://www.yehudakatz.com/) was clever enough to make an [automated solution to this problem](http://github.com/wycats/textmate/tree/master). It handles all these cases: fetching the source, putting it in the right place, and reloading TextMate.

It's available on [GitHub](http://github.com) as a gem, so you'd think you'd be able

    sudo gem install wycats-textmate

Not so much. The executable the gem seems broken. Let's install it by hand:

    git clone git://github.com/wycats/textmate.git
    cd textmate
    rake gem
    sudo gem install pkg/*.gem

Now let's see what's available:

    textmate remote

Lots of goodies. Let me just install a few for the stuff I regularly use...

    textmate install "Ruby Haml" GitHub
    textmate install "Ruby Shoulda" GitHub
    textmate install HTML GitHub
    textmate install Git GitHub
    textmate install "Javascript Jquery" GitHub
    textmate install "Nginx" GitHub
    textmate install "Ruby C Extensions" GitHub
    textmate install "Ruby on Rails" GitHub

If you happened to installed one of these in the past, you'll need to uninstall it first.

    textmate uninstall "Ruby on Rails"
    
That's all there is to it.
