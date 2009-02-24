--- 
permalink: /posts/autoloading-factory_girl-definitions.html
title: Autoloading factory_girl definitions
tags: 
- factory_girl
- rails
- ruby
- testing
layout: post
---
I'd like to point out a feature that silently made it into [factory\_girl](http://thoughtbot.com/projects/factory_girl) as of 1.1.4: autoloading of factory definitions.

If you create `test/factories.rb`, `spec/factories.rb`, they will automatically be required, making your factories available to your tests or specs.

Additionally, you can create directories named `test/factories` and `spec/factories`, and any `.rb` files will be required as well. I generally prefer this way, and name factories like `urmodel_factory.rb`, such that fuzzy finding files makes it easy to open up a specific factory.

This functionality was part the [factory\_girl\_on\_rails](http://github.com/technicalpickles/factory_girl_on_rails/tree/master) plugin I wrote several months ago before there was any autoloading. Once I got annoyed by having to install a plugin in addition to a gem, I found a way to do it inside of factory_girl herself, and you can now benefit from that work.

Relatedly, you can consider factory\_girl\_on\_rails deprecated in favor of the built-in factory_girl functionality.