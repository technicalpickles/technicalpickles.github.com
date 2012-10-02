--- 
title: A walk through of test-driven development with shoulda
tags: 
- rails
- ruby
- shoulda
- shoulda-generators
- testing
layout: post
---
I'm not sure if you've heard, but [shoulda](http://thoughtbot.com/projects/shoulda) makes [test-driven development](http://en.wikipedia.org/wiki/Test-driven_development) really simple.

Last week's boston.rb focused on testing, where we split up into two groups. One group worked on adding a test suite to an app that had no tests, and my group was using test-driven development for adding new features to the [boston.rb](http://bostonrb.org) website.

I figured I'd do recap how we used TDD that night.

### shoulda-generators, a quick plug

One thing I'm going to omit from the walkthrough is that a chunk of time was spent converting the scaffold-generated test from test-unit to shoulda, and from erb to haml. While it was a worthwhile detour for some present, it was a little annoying. If only there were shoulda and haml generators, I say!

Struck awake by inspiration at 5am the next day, I took fate into my own hands...

[BEHOLD!](http://github.com/technicalpickles/shoulda_generator/tree/master)

It is available as a plugin for now, but I'll try to gemify it sometime.

While we didn't have the luxury of these generators at the time, I'm going to use them now for the sake of this writeup.

### The new feature: Places

Before we start, let's just make sure the tests work... alright, good.

We want to add functionality for 'places.' A `Place` is somewhere in the area that's somehow connected to Ruby: a business using it, a venue for a meeting, an incubator, things like that.

A `Place` probably has:

 * A name
 * An address
 * A description

Let's generate the scaffold:

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">script/generate shoulda_scaffold place name:string address:string description:text</kbd>
<samp>      exists  app/models/
      exists  app/controllers/
      exists  app/helpers/
      create  app/views/places
      exists  app/views/layouts/
      exists  test/functional/
      exists  test/unit/
      create  public/stylesheets/blueprint
      create  app/views/places/index.html.haml
      create  app/views/places/show.html.haml
      create  app/views/places/new.html.haml
      create  app/views/places/edit.html.haml
      create  app/views/places/_form.html.haml
      create  app/views/layouts/places.html.haml
      create  public/stylesheets/blueprint/print.css
      create  public/stylesheets/blueprint/screen.css
      create  public/stylesheets/blueprint/ie.css
      create  app/controllers/places_controller.rb
      create  test/functional/places_controller_test.rb
      create  app/helpers/places_helper.rb
       route  map.resources :places
  dependency  shoulda_model
      exists    app/models/
      exists    test/unit/
      create    test/factories/
      create    app/models/place.rb
      create    test/unit/place_test.rb
      create    test/factories/place.rb
      create    db/migrate
      create    db/migrate/20080620011343_create_places.rb</samp></pre>

Let's try the tests again.

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">rake test</kbd>
<samp>(in /Users/nichoj/Projects/bostonrb)
You have 1 pending migrations:
  20080620011343 CreatePlaces
Run "rake db:migrate" to update your database then try again.</samp></pre>

Whoops, need to migrate first.

<pre><samp class="prompt shell">$</samp> <kbd>rake db:migrate</kbd>
<samp>(in /Users/nichoj/Projects/bostonrb)
== 20080620011343 CreatePlaces: migrating =====================================
-- create_table(:places)
   -&gt; 0.0171s
==</samp>
<samp class="prompt shell">$</samp> <kbd class="shell">rake test</kbd>
<samp>(in /Users/nichoj/Projects/bostonrb)
/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby -Ilib:test &quot;/Library/Ruby/Gems/1.8/gems/rake-0.8.1/lib/rake/rake_test_loader.rb&quot; &quot;test/unit/place_test.rb&quot; 
Loaded suite /Library/Ruby/Gems/1.8/gems/rake-0.8.1/lib/rake/rake_test_loader
Started
...
Finished in 0.147509 seconds.

3 tests, 3 assertions, 0 failures, 0 errors
/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby -Ilib:test &quot;/Library/Ruby/Gems/1.8/gems/rake-0.8.1/lib/rake/rake_test_loader.rb&quot; &quot;test/functional/places_controller_test.rb&quot; 
Loaded suite /Library/Ruby/Gems/1.8/gems/rake-0.8.1/lib/rake/rake_test_loader
Started
...........
Finished in 0.293275 seconds.

11 tests, 14 assertions, 0 failures, 0 errors
/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby -Ilib:test &quot;/Library/Ruby/Gems/1.8/gems/rake-0.8.1/lib/rake/rake_test_loader.rb&quot;</samp></pre>

Good so far.

Now what do we want to do with the place? Those attributes should be required. Open the test, and see what we have so far:

{% highlight ruby %}
class PlaceTest < ActiveSupport::TestCase
  should_have_db_column :name
  should_have_db_column :address
  should_have_db_column :description
end
{% endhighlight %}

Oh, cool. The generated test already has a few `shoulds` in there. Now we want to add `should_require_attributes`.

{% highlight ruby %}
class PlaceTest < ActiveSupport::TestCase
  should_have_db_column :name
  should_have_db_column :address
  should_have_db_column :description
  should_require_attributes :name, :address, :description
end
{% endhighlight %}

And let's see how this does...

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">ruby test/unit/place_test.rb</kbd>
<samp>Loaded suite test/unit/place_test
Started
...FFF
Finished in 0.154072 seconds.

  1) Failure:
test: Place should require address to be set. (PlaceTest)
    [/Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/active_record_helpers.rb:41:in `__bind_1213925922_190232'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `call'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `test: Place should require address to be set. '
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `__send__'
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `run']:
Place does not require address.
&lt;false&gt; is not true.

  2) Failure:
test: Place should require description to be set. (PlaceTest)
    [/Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/active_record_helpers.rb:41:in `__bind_1213925922_194386'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `call'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `test: Place should require description to be set. '
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `__send__'
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `run']:
Place does not require description.
&lt;false&gt; is not true.

  3) Failure:
test: Place should require name to be set. (PlaceTest)
    [/Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/active_record_helpers.rb:41:in `__bind_1213925922_196154'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `call'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `test: Place should require name to be set. '
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `__send__'
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `run']:
Place does not require name.
&lt;false&gt; is not true.

6 tests, 6 assertions, 3 failures, 0 errors</samp></pre>

Ok, this is to be expected. We didn't actually `validate_presence_of` the fields. Let's do that now:

{% highlight ruby %}
class Place &lt; ActiveRecord::Base
  validates_presence_of :name
  validates_presence_of :address
  validates_presence_of :description
end
{% endhighlight %}

If we try it again...

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd>ruby test/unit/place_test.rb</kbd>
<samp>Loaded suite test/unit/place_test
Started
......
Finished in 0.153653 seconds.

6 tests, 12 assertions, 0 failures, 0 errors</samp></pre>

Test-driven, wooo!!! I think this is good enough to commit.

### Relating places and events

We already have an `Event` model, so I figure a `Place` should be able to have events, and event would have could be at a particular place. Shall we test it?

{% highlight ruby %}
class PlaceTest < ActiveSupport::TestCase
  # omitted...
  should_have_many :events
end
{% endhighlight %}

We get a failure though:

<pre>  1) Failure:
test: Place should have many events. (PlaceTest)
    [/Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/active_record_helpers.rb:378:in `__bind_1213931312_854475'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `call'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `test: Place should have many events. '
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `__send__'
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `run']:
Place does not have any relationship to events.
&lt;nil&gt; is not true.</pre>

Now we add the `has_many` to `Place`. But we still have a failure!

<pre> 1) Failure:
test: Place should have many events. (PlaceTest)
    [/Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/active_record_helpers.rb:405:in `__bind_1213931441_769681'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `call'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `test: Place should have many events. '
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `__send__'
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `run']:
Event does not have a place_id foreign key.
&lt;false&gt; is not true.</pre>

Oh, probably should migrate something.

<pre class="terminal unix"><samp class="prompt shell">$</samp> <kbd class="shell">script/generate migration place_event_relationship</kbd>
<samp>      exists  db/migrate
      create  db/migrate/20080620031448_place_event_relationship.rb</samp></pre>

And we make it look like:

{% highlight ruby %}
class PlaceEventRelationship &lt; ActiveRecord::Migration
  def self.up
    add_column :events, :place_id, :integer
  end

  def self.down
    remove_column :events, :place_id
  end
end
{% endhighlight %}

Do a `rake db:migrate db:test:prepare`, and we're ready to run it again. Wait for it... wait for it...

    $ ruby test/unit/place_test.rb 
    Loaded suite test/unit/place_test
    Started
    .......
    Finished in 0.159728 seconds.
    
    7 tests, 15 assertions, 0 failures, 0 errors

Let's add a test for the other side of the relationship:

{% highlight ruby %}
class EventTest < ActiveSupport::TestCase
  # ... snip ...
  should_belong_to :place
end
{% endhighlight %}

Failures, as expected.

<pre> 1) Failure:
test: Event should belong_to place. (EventTest)
    [/Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/active_record_helpers.rb:476:in `__bind_1213933933_768823'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `call'
     /Users/nichoj/Projects/bostonrb/vendor/plugins/shoulda/lib/shoulda/gem/shoulda.rb:191:in `test: Event should belong_to place. '
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `__send__'
     /Library/Ruby/Gems/1.8/gems/activesupport-2.1.0/lib/active_support/testing/setup_and_teardown.rb:67:in `run']:
Event does not have any relationship to place.
&lt;nil&gt; is not true.</pre>

And now we actually implement it...

{% highlight ruby %}
class Event < ActiveRecord::Base
  belongs_to :place
end
{% endhighlight %}

And guess what? They pass. We win at things that are winnable, like TDD.

### Summary

These have been pretty trivial examples, but it demonstrates how to roll with TDD.

 * Write a test
 * See it fail
 * Write code
 * See the test pass
 * Repeat

It also demonstrates how shoulda lets tersely test things that can be written tersely with Rails.

## Update

The repository was renamed [shoulda\_generator](http://github.com/technicalpickles/shoulda_generator/tree/master). It is also now installable as a gem:

    $ gem sources -a http://gems.github.com
    $ sudo gem install technicalpickles-shoulda_generator

