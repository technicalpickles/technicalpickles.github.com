--- 
title: shoulda 2.0.0, now with gem power
tags: 
- gem
- plugin
- rails
- ruby
- shoulda
- testing
layout: default
---
[Shoulda](http://thoughtbot.com/projects/shoulda) has long been available as a plugin. It's core was yanked out at some point, and made available as a gem. Unfortunately, the codebases diverged, with main development occurring in the plugin.

What was once was divided is now united, as of this day. Shoulda 2.0.0. Check it:

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">gem install thoughtbot-shoulda</kbd></pre>
    
Nothing new there, but you can use this gem in a Rails app:

<pre><code class="ruby"># in config/environment.rb
Rails::Initializer.run do |config|
  config.gem 'thoughtbot-shoulda', :lib => 'shoulda/rails', :source => 'http://gems.github.com'
end</code></pre>
    
Using gems instead of plugins isn't perfect though, specifically, [rake tasks don't get loaded yet](http://rails.lighthouseapp.com/projects/8994/tickets/59-when-loading-a-plugin-via-rubygems-rake-tasks-aren-t-included-2). Hopefully that will be fixed eventually, but in the mean time, you can do:

<pre><code class="ruby"># in Rakefile
begin require 'shoulda/tasks' rescue LoadError end</code></pre>

You can also use this gem in any non-Rails app. In your `test_helper.rb`, you'd just do something like:

<pre><code class="ruby">require 'shoulda'</code></pre>

So what else is new? I'm not sure exactly what was in the last release, so I'll leave that for a more official announcement.