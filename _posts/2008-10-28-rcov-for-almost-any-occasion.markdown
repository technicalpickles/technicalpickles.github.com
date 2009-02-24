--- 
permalink: /posts/rcov-for-almost-any-occasion
title: RCov for almost any occasion
tags: 
- rcov
- ruby
- testing
layout: post
---
I hope I don't have to tell you why it's awesome to be testing. I'll take that as a given.

Once you're testing, it is useful to be able to see much of your code is being touched by your tests. This is where [rcov](http://eigenclass.org/hiki.rb?rcov) makes an entrance. It does some sorcery in order to do just that: tell you what lines of your code your tests ran.

I'm not going to talk about the pros/cons of using code coverage, just going to tell you how to use rcov in most Ruby projects you might run into. For further reading, here's some linkly love to get you started:

 * [Code coverage on wikipedia](http://en.wikipedia.org/wiki/Code_coverage)
 * [How effective is code coverage? on LinkedIn Answers](http://www.linkedin.com/answers/technology/software-development/TCH_SFT/250828-155544)
 
I'm mainly putting this together because I always had trouble finding it myself, and figured it'd be useful to have for future reference.

### Get ur rcov right here

The main rcov website can be found [here](http://eigenclass.org/hiki.rb?rcov). I have seen some annoying segfault problems when running that version, so have taken to using [this variation on GitHub](http://github.com/spicycode/rcov/tree). Install it with:

    # Run the following if you haven't already:
    gem sources -a http://gems.github.com
    # Install the gem:
    sudo gem install spicycode-rcov

### Using it

I've wanted to use rcov in three different settings. I'm sure there's more, but it's what I've dealt with so far.

 * A RubyGem project using Test::Unit
 * A RubyGem project using RSpec
 * A Rails project

### RubyGem using Rake and Test::Unit

You just need to update your `Rakefile` to include something like:

<pre><code class="ruby">require 'rcov/rcovtask'
Rcov::RcovTask.new do |t|
  t.libs &lt;&lt; &quot;test&quot;
  t.test_files = FileList['test/*_test.rb']
  t.verbose = true
end</code></pre>

Now you can run `rake rcov` and then see the report at `coverage/index.html`.

### RubyGem using Rake and RSpec

Similarly, you just need to update your `Rakefile` to do:

<pre><code class="ruby">require 'rspec/spec_task'
Spec::Rake::SpecTask.new(&quot;rcov_spec&quot;) do |t|
  t.spec_files = FileList['spec/**/*_spec.rb']
  t.spec_opts = ['--color']
  t.rcov = true
  t.rcov_opts = ['--exclude', '^spec,/gems/']
end</code></pre>
    
Now you can run `rake rcov_spec` and then see the report at `coverage/index.html`.

### Rails Project 

There's several ways out there of including coverage in your Rails app. I found the most direct was just to use the [metric_fu](http://github.com/jscruggs/metric_fu/tree/master) plugin.

    script/plugin install git://github.com/jscruggs/metric_fu.git
    
After it's installed, among other things, you can run `rake metrics:coverage` and see the report at `metrics/coverage/index.html`.

### Updates

Had a few things pointed out:

 * Rcov::RcovTask is in 'rcov/rcovtask', not 'rcov/rcov_task'. Caught by hardbap.
 * The origin glob for that task only caught files in `test/*_test.rb`. `test/**/*_test.rb` would catch subdirectories too. Caught by fowlduck.
 * fowlduck found some problems when using metric_fu as a gem, and posted work arounds in the comments.