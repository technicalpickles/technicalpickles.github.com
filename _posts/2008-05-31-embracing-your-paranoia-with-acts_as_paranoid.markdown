--- 
permalink: /posts/embracing-your-paranoia-with-acts_as_paranoid.html
title: Embracing your paranoia with acts_as_paranoid
tags: 
- acts_as_paranoid
- rails
- ruby
layout: post
---
According to their [RDoc](http://ar-paranoid.rubyforge.org/), "Overrides some basic methods for the current model so that calling destroy sets a 'deleted\_at' field to the current timestamp. ActiveRecord is required."

There doesn't seem to be really good up-to-date documentation on how to actually use it though. Let's take a stab at it.

### Installing

First thing is to install it. I typically use piston to manage them. We need the [at least version 1.9.x](http://blog.teksol.info/2008/3/15/piston-2-0-progress-piston-can-import-all-four-cases) for its git support.

<pre class="terminal unix"><samp class="shell prompt">myapp $</samp> <kbd class="shell">cd vendor/plugins</kbd>
<samp class="shell prompt">myapp/vendor/gems $</samp> <kbd class="shell"> piston import git://github.com/technoweenie/acts_as_paranoid.git</kbd>
<samp>INFO main: Guessing the repository type
INFO main: Guessing the working copy type
INFO main: Checking out the repository
INFO main: Copying from Piston::Revision(git://github.com/technoweenie/acts_as_paranoid.git@1e9144e483524b4f246a1768462eadb22f634f19)
INFO main: Checked out "git://github.com/technoweenie/acts_as_paranoid.git" 1e9144e to "acts_as_paranoid"</samp></pre>

### Configuring 

We now can define a model class to be paranoid:

{% highlight ruby %}
class Event < ActiveRecord::Base  
  acts_as_paranoid
end
{% endhighlight %}

Internally, acts\_as\_paranoid uses the `deleted_at` column to track if something is deleted or not. So, we need to make a migration for it:

<pre class="terminal unix"><samp class="shell prompt">myapp $</samp> <kbd class="shell">script/generate migration MakeEventParanoid</kbd>
<samp>  exists  db/migrate
  create  db/migrate/012_make_event_paranoid.rb</samp></pre>

It should look something like:

{% highlight ruby %}
class MakeEventParanoid &lt; ActiveRecord::Migration
  def self.up
    add_column :events, :deleted_at, :datetime
  end

  def self.down
    remove_column :events, :deleted_at
  end
end
{% endhighlight %}

You can now migrate:

<pre class="terminal unix"><samp class="shell prompt">myapp $</samp> <kbd class="shell">rake db:migrate</kbd>
<samp>== 12 AddDeletedAt: migrating =================================================
-- add_column("events", :deleted_at, :datetime)
   -> 0.3596s
== 12 AddDeletedAt: migrated (0.3596s) ========================================</samp></pre>

### Using

It might be easiest to demonstrate with this snippet from `script/console`:

<pre>&gt;&gt; event = Event.find(:first)
=&gt; #&lt;Event id: 91133883, date: &quot;2008-04-28 00:00:00&quot;, description: &quot;The Ezra event tonight has been cancelled.The Ezra ...&quot;, created_at: &quot;2008-04-16 13:30:20&quot;, updated_at: &quot;2008-05-30 23:43:22&quot;, location: &quot;&quot;, lng: nil, lat: nil, title: &quot;Meeting&quot;, deleted_at: nil&gt;
&gt;&gt; event.destroy!
=&gt; #&lt;Event id: 91133883, date: &quot;2008-04-28 00:00:00&quot;, description: &quot;The Ezra event tonight has been cancelled.The Ezra ...&quot;, created_at: &quot;2008-04-16 13:30:20&quot;, updated_at: &quot;2008-05-30 23:43:22&quot;, location: &quot;&quot;, lng: nil, lat: nil, title: &quot;Meeting&quot;, deleted_at: nil&gt;
&gt;&gt; Event.find(:first)
=&gt; #&lt;Event id: 420662881, date: &quot;2008-04-15 00:00:00&quot;, description: &quot;Worked on creating the app for this site, [bostonrb...&quot;, created_at: &quot;2008-04-16 13:30:20&quot;, updated_at: &quot;2008-05-29 01:19:41&quot;, location: nil, lng: nil, lat: nil, title: &quot;Hackfest&quot;, deleted_at: nil&gt;
&gt;&gt; Event.find_with_deleted(:first)
=&gt; #&lt;Event id: 420662881, date: &quot;2008-04-15 00:00:00&quot;, description: &quot;Worked on creating the app for this site, [bostonrb...&quot;, created_at: &quot;2008-04-16 13:30:20&quot;, updated_at: &quot;2008-05-29 01:19:41&quot;, location: nil, lng: nil, lat: nil, title: &quot;Hackfest&quot;, deleted_at: nil&gt;
&gt;&gt; event.deleted_at = nil
=&gt; nil
&gt;&gt; event.save!
=&gt; true
&gt;&gt; Event.find(:first)
=&gt; #&lt;Event id: 420662881, date: &quot;2008-04-15 00:00:00&quot;, description: &quot;Worked on creating the app for this site, [bostonrb...&quot;, created_at: &quot;2008-04-16 13:30:20&quot;, updated_at: &quot;2008-05-29 01:19:41&quot;, location: nil, lng: nil, lat: nil, title: &quot;Hackfest&quot;, deleted_at: nil&gt;</pre>

A synopsis:
 * Find the first event
 * Delete it
 * Find the first event again, and see that it's something else
 * Find with deleted records, to see our original event
 * Unset deleted\_at to 'undelete' the orignal event.

__Update:__ Fixed the `script/console` section. Was using the wrong variable that I used earlier in the IRB session, but wasn't displayed. 
