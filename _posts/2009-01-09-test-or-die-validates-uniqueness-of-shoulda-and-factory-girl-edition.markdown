--- 
permalink: /posts/test-or-die-validates-uniqueness-of-shoulda-and-factory-girl-edition.html
title: " Test Or Die: Validates Uniqueness Of, Shoulda and Factory Girl Edition"
tags: 
- model
- rails
- ruby
- shoulda
- testing
- testordie
layout: post
---
[John Nunemaker](http://railstips.org) recently posted about [testing `validates_uniqueness_of` using test/unit and fixtures](http://railstips.org/2009/1/8/test-or-die-validates-uniqueness-of). I'm going to take his lead, but use [shoulda](http://thoughtbot.com/projects/shoulda) and [factory_girl](http://thoughtbot.com/projects/factory_girl) instead. I'm going to assume you've read his article.

If you look at the code for testing `validates_uniqueness_of` of your model, and think about how often you might use it in your projects, it starts to be repetitive very quickly. John has this to say about this style of testing in his comments:

> "_The thing that I have noticed when teaching people testing though is they have to learn this first. If they start with the macros, mocking and stubbing, they never quite understand the underpinnings. Also, when they start new project, I often watch them stumble, trying to do things with all the macros, but not having their test environment setup. I think it is good for people to see that they are repeating themselves and think, well, how can I fix this? I kind of think it is good to feel the repetition pain as it helps people understand why we switch to macros and start to stub and mock things so that you don’t require hitting the database or using fixtures._"

Now, I'm assuming you've learned these valuable lessons, and want to more succinctly test your code.

Before starting, you'll need to install should and factory\_girl. I also created [shoulda\_generator](http://github.com/technicalpickles/shoulda_generator/tree/master) for shouldaified and factory_girlied generators.

You can either install shoulda and factory\_girl as plugins by running:

    script/plugin install git://github.com/thoughtbot/shoulda.git 
    script/plugin install git://github.com/thoughtbot/factory_girl.git
    
... or as gems by adding this to `config/environment.rb` (although some might argue it belongs in `config/environments/test.rb`):

<pre><code class="ruby">config.gem 'thoughtbot-shoulda', 
  :lib    =&gt; 'shoulda',
  :source =&gt; 'http://gems.github.com'
config.gem 'thoughtbot-factory_girl', 
  :lib    =&gt; 'factory_girl', 
  :source =&gt; 'http://gems.github.com'</code></pre>
      
shoulda\_generator can be installed as a gem:

    # run this if you haven't before:
    gem sources -a http://gems.github.com
    # install it:
    sudo gem install technicalpickles-shoulda_generator
    
We can now proceed:

    script/generate shoulda_model Category name:string
    rake db:migrate db:test:prepare

Shoulda provides the `should_require_unique_attributes` macro for testing uniqueness. It requires an existing record though, so we need to use `factory_girl` to create one in a context. Together, this would look like:

<pre><code class="ruby">require File.dirname(__FILE__) + '/../test_helper'

class CategoryTest &lt; ActiveSupport::TestCase
  context &quot;an existing category&quot; do
    setup do
      @category = Factory(:category)
    end

    should_require_unique_attributes :name
  end
end</code></pre>
    
If we run `rake test`, we'll see it fails because we haven't declared name to be unique:

    Started
    F
    Finished in 0.096949 seconds.

      1) Failure:
    test: an existing category should require unique value for name. (CategoryTest)
        [/opt/ruby-enterprise-1.8.6-20080810/lib/ruby/gems/1.8/gems/thoughtbot-shoulda-2.0.6/lib/shoulda/active_record/assertions.rb:63:in `assert_bad_value'
         /opt/ruby-enterprise-1.8.6-20080810/lib/ruby/gems/1.8/gems/thoughtbot-shoulda-2.0.6/lib/shoulda/active_record/macros.rb:109:in `__bind_1231428798_266017'
         /opt/ruby-enterprise-1.8.6-20080810/lib/ruby/gems/1.8/gems/thoughtbot-shoulda-2.0.6/lib/shoulda/context.rb:254:in `call'
         /opt/ruby-enterprise-1.8.6-20080810/lib/ruby/gems/1.8/gems/thoughtbot-shoulda-2.0.6/lib/shoulda/context.rb:254:in `test: an existing category should require unique value for name. '
         /opt/ruby-enterprise-1.8.6-20080810/lib/ruby/gems/1.8/gems/activesupport-2.2.2/lib/active_support/testing/setup_and_teardown.rb:94:in `__send__'
         /opt/ruby-enterprise-1.8.6-20080810/lib/ruby/gems/1.8/gems/activesupport-2.2.2/lib/active_support/testing/setup_and_teardown.rb:94:in `run']:
    Category allowed "MyString" as a value for name.
    <false> is not true.
    
    1 tests, 2 assertions, 1 failures, 0 errors

Now add the `validates_uniqueness_of`:

<pre><code class="ruby">class Category &lt; ActiveRecord::Base
  validates_uniqueness_of :name
end</code></pre>

And running `rake test` now yields much success:

    Started
    .
    Finished in 0.132653 seconds.

    1 tests, 4 assertions, 0 failures, 0 errors

I'd continue on to test `validates_uniqueness_of :name, :scoped_to => :site_id`, but I haven't actually used it before, and I wasn't able to get the macro working quite right.
    
The thing to notice about shoulda is that many times, if there's a one-liner for you model, there's a one-liner for testing that behavior. Consider [John's original code using test/unit](http://railstips.org/2009/1/8/test-or-die-validates-uniqueness-of). That's quite a few lines for testing one line. Shoulda tries to adopt "best practices" for testing things this kind of thing, and expose it as a one line method call. If you took the more verbose test/unit version, refactored and generalized it over time, I don't doubt it would be very similar to `should_require_unique_attributes` implementation wise.