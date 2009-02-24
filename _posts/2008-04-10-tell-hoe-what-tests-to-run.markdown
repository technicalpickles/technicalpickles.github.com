--- 
permalink: /posts/tell-hoe-what-tests-to-run.html
title: Tell Hoe what tests to run
tags: 
- hoe
- rake
- ruby
- rubygem
layout: post
---
I've been recently playing around with a Ruby frontend to the [del.ico.us API](http://del.icio.us/help/api/). I decided to use [Hoe](http://seattlerb.rubyforge.org/hoe/) to get things rolling with a proper project layout.

It's pretty straightforward to use initially:

    $ sow deliciousr
    
This generates everything you need to start.

    $ cd deliciousr
    $ rake -T
    rake announce         # Create news email file and post to rubyforge.
    rake audit            # Run ZenTest against the package.
    rake check_manifest   # Verify the manifest.
    rake clean            # Clean up all the extras.
    rake clobber_docs     # Remove rdoc products
    rake clobber_package  # Remove package products
    rake config_hoe       # Create a fresh ~/.hoerc file.
    rake debug_gem        # Show information about the gem.
    rake default          # Run the default tasks.
    rake docs             # Build the docs HTML Files
    rake email            # Generate email announcement file.
    rake gem              # Build the gem file deliciousr-1.0.0.gem
    rake generate_key     # Generate a key for signing your gems.
    rake install_gem      # Install the package as a gem.
    rake multi            # Run the test suite using multiruby.
    rake package          # Build all the packages
    rake post_blog        # Post announcement to blog.
    rake post_news        # Post announcement to rubyforge.
    rake publish_docs     # Publish RDoc to RubyForge.
    rake redocs           # Force a rebuild of the RDOC files
    rake release          # Package and upload the release to rubyforge.
    rake repackage        # Force a rebuild of the package files
    rake ridocs           # Generate ri locally for testing.
    rake test             # Run the test suite.
    rake test_deps        # Show which test files fail when run alone.

You get a lot of stuff for free here. So I went about happily coding, and of course, testing. Initially, I was just running the tests by invoking them directly, or using [TextMate](http://macromates.com/)'s Ruby bundle. But, I figured it'd be best to be running it by using the free rake task.

    $ rake test
    (in /Users/nichoj/Projects/deliciousr)
    /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby -w -Ilib:ext:bin:test -e 'require "test/unit"; require "test/test_helper.rb"' 
    ./test/../lib/deliciousr/v1/api_call.rb:64: warning: method redefined; discarding old http
    Loaded suite .
    Started
    
    Finished in 0.000223 seconds.
    
    0 tests, 0 assertions, 0 failures, 0 errors

Uh, why aren't my tests running???

The short of it is that hoe looks for a certain filename pattern, specifically, it likes tests to be like:

    test/**/test_*.rb

My tests? They might be named more like:

    test/**/*_test.rb

So, ideally I would rename my tests, but fortunately there is a quick fix. In your Rakefile, you need to add a line to the Hoe configuration.

<pre><code class="ruby">Hoe.new('deliciousr', Deliciousr::VERSION) do |p|
  # snip
  p.test_globs = 'test/**/*_test.rb'
  # snip
end</code></pre>

It was only by digging into the source that I found it.

Remember kiddies, __the source shall set you free__.