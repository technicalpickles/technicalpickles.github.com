--- 
permalink: /posts/managing-svn-ignore-with-impunity.html
title: Managing svn:ignore with impunity
tags: 
- rails
- rake
- ruby
- subversion
layout: post
---
Managing `svn:ignore` has always annoyed me. After using git for the past few months, it bugs me even __more__ now.

I think I've come up with a simple way of coping. That, or I totally stole it from someone else on the internet, without realizing it. It's hard to keep track of these kinds of things.

## The trick

Here's what you do:

 * Create a .svnignore in the top of your project.
 * Make a list of files/wildcards you care to ignore
 * Save it
 
Now, from the top of your project, you can do:

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">svn propset svn:ignore -F .svnignore  .</kbd>
</pre>
    
If one were to wordify this command, it's be something like "Set the svn:ignore property from the contents of the `.svnignore` onto the current directory." It can really be any file, but I kinda like this convention.

It is also likely you will want to keep `.svnignore` under source control.

## Now automate it

It's usually best to automate when possible, since even this simplied way of managing ignored files might get tedious. Or maybe someone coming onto the project doesn't have our level svn-fu.

We already have a `lib/tasks/svn.rake` that has a few tricks in it, so I figured on expanding it. Here's the original:

<pre><code class="ruby">namespace :svn do
  desc "Adds all files with an svn status flag of '?'"
  task(:add) { system %q(svn status | awk '/\\?/ {print $2}' | xargs svn add) }
  
  desc "Deletes all files with an svn status flag of '!'"
  task(:delete) { system %q(svn status | awk '/\\!/ {print $2}' | xargs svn delete) }
  
  desc "Writes the log file to doc/svn_log.txt"
  task(:log) do
    File.delete("#{RAILS_ROOT}/doc/svn_log.txt") if File::exists?("#{RAILS_ROOT}/doc/svn_log.txt")
    File.new("#{RAILS_ROOT}/doc/svn_log.txt", "w+")
    system("svn log >> doc/svn_log.txt")
  end
end</code></pre>

I added a new task that looks like:

<pre><code class="ruby">desc 'Updates svn:ignore from .svnignore'
task(:update_svn_ignore) do
  system %q(svn propset svn:ignore -F .svnignore .)
end</code></pre>

Now we can tweak `.svnignore` to our hearts' content, and then just run:

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">rake svn:update_svn_ignore</kbd>
<samp>(in /Users/nichoj/Projects/boston_rb)
property 'svn:ignore' set on '.'</samp>
</pre>
    
Awesome.