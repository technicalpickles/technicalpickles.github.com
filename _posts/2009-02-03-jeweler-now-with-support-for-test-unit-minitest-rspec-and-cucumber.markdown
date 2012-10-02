--- 
title: Jeweler, now with support for test/unit, minitest, rspec, and cucumber
tags: 
- jeweler
- minitest
- rspec
- ruby
- shoulda
- test/unit
- testing
layout: post
---
I had announced [jeweler](http://github.com/technicalpickles/jeweler) [a few weeks ago](http://technicalpickles.com/posts/craft-the-perfect-gem-with-jeweler), and it's been very well received. It's even the [GitHub](http://github.com) recommended way of managing your gems, which is pretty awesome.

Things haven't been sitting still though. Version [0.8.1](http://github.com/technicalpickles/jeweler/tree/v0.8.1) is now released. Highlights include:

 * Support for generating projects with [test/unit](http://www.ruby-doc.org/stdlib/libdoc/test/unit/rdoc/classes/Test/Unit.html), [minitest](http://blog.zenspider.com/minitest/), and [rspec](http://rspec.info/) in addition to [shoulda](http://github.com/thoughtbot/shoulda) and [bacon](http://github.com/chneukirchen/bacon/).
 * Support for [cucumber](http://cukes.info/) stories
 
You can get it using the usual gem install command:

    # Run the following if you haven't already:
    gem sources -a http://gems.github.com
    # Install the gem:
    sudo gem install technicalpickles-jeweler
    
See the `jeweler` command for possible arguments:

    Usage: jeweler [options] reponame
    e.g. jeweler the-perfect-gem
            --bacon                      generate bacon specs
            --shoulda                    generate shoulda tests
            --testunit                   generate test/unit tests
            --miniunit                   generate mini/unit tests
            --rspec                      generate rspec tests
            --create-repo                create the repository on GitHub
            --summary [SUMMARY]          specify the summary of the project
        -h, --help                       display this help and exit
 
That is all for now, so get crafting!
