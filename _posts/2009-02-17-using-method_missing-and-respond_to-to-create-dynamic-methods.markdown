--- 
title: Using method_missing and respond_to? to create dynamic methods
tags: 
- metaprogramming
- method_missing
- respond_to
- rspec
- ruby
layout: post
---
`method_missing` is a well-known tool in the Ruby metaprogramming toolbox. It's callback method you can implement that gets called when a object tries to call a method that's, well, missing. A well known example of this is ActiveRecord dynamic finders. For example, if your `User` has an `email` attribute, you can `User.find_by_email('joe@example.com')` even though `User` nor `ActiveRecord::Base` have defined it.

`method_missing`'s cousin, `respond_to?`, is often forgotten though. `respond_to?` is used to determine if an object responds to a method. It is often used to check that an object knows about a method before actually calling it, in order to avoid an error at runtime about the method existing.

To have a consistent API when using `method_missing`, it's important to implement a corresponding `respond_to?`.

### Example

Imagine we have a `Legislator` class. We want a dynamic finder that will translate `find_by_first_name('John')` to `find(:first_name => 'John')`. Here's a first pass:

{% highlight ruby %}
class Legislator
  # Pretend this is a real implementation
  def find(conditions = {})
  end
  
  # Define on self, since it's  a class method
  def self.method_missing(method_sym, *arguments, &block)
    # the first argument is a Symbol, so you need to_s it if you want to pattern match
    if method_sym.to_s =~ /^find_by_(.*)$/
      find($1.to_sym => arguments.first)
    else
      super
    end
  end
end
{% endhighlight %}

That seems to do the trick. But `Legislator.respond_to?(:find_by_first_name)` would return `false`. Let's add `respond_to?` to fix it.

{% highlight ruby %}
class Legislator
  # ommitted
  
  # It's important to know Object defines respond_to to take two parameters: the method to check, and whether to include private methods
  # http://www.ruby-doc.org/core/classes/Object.html#M000333
  def self.respond_to?(method_sym, include_private = false)
    if method_sym.to_s =~ /^find_by_(.*)$/
      true
    else
      super
    end
  end
end
{% endhighlight %}
    
As commented, `respond_to?` takes two parameters. If you don't do this, there's a chance some library may expect to be able to invoke `respond_to?` with 2 parameters, resulting in an `ArgumentError`. In particular, the RSpec mocking library does this exactly this, and caused me a bit of befuddlement until I turned on full backtraces.

### Possible refactorings

#### DRY

You might notice it's not very DRY. We use the same pattern matching twice. This particular logic is simple, but it could be grow to be more complicated.

We can again look to ActiveRecord for inspiration. It encapsulates the logic in [ActiveRecord::DynamicFinderMatch](http://api.rubyonrails.org/classes/ActiveRecord/DynamicFinderMatch.html), to avoid repetition between `method_missing` and `respond_to?`, in addition to simplifying the methods.


{% highlight ruby %}
class LegislatorDynamicFinderMatch
  attr_accessor :attribute
  def initialize(method_sym)
    if method_sym.to_s =~ /^find_by_(.*)$/
      @attribute = $1.to_sym
    end
  end
  
  def match?
    @attribute != nil
  end
end

class Legislator
  def self.method_missing(method_sym, *arguments, &block)
    match = LegislatorDynamicFinderMatch.new(method_sym)
    if match.match?
      find(match.attribute => arguments.first)
    else
      super
    end
  end

  def self.respond_to?(method_sym, include_private = false)
    if LegislatorDynamicFinderMatch.new(method_sym).match?
      true
    else
      super
    end
  end
end
{% endhighlight %}

#### Caching `method_missing`

Always passing through `method_missing` [can be slow](http://www.jroller.com/dscataglini/entry/speeding_up_method_missing).

Another lesson we can take from is ActiveRecord is defining the method during `method_missing`, and then `send` to the now-defined method.

{% highlight ruby %}
class Legislator    
  def self.method_missing(method_sym, *arguments, &block)
    match = LegislatorDynamicFinderMatch.new(method_sym)
    if match.match?
      define_dynamic_finder(method_sym, match.attribute)
      send(method_sym, arguments.first)
    else
      super
    end
  end
  
  protected
  
  def self.define_dynamic_finder(finder, attribute)
    class_eval <<-RUBY
      def self.#{finder}(#{attribute})        # def self.find_by_first_name(first_name)
        find(:#{attribute} => #{attribute})   #   find(:first_name => first_name)
      end                                     # end
    RUBY
  end
end
{% endhighlight %}

### Testing

Being the test-minded individual that I am, no code would be complete without some testing.

Creating the `LegislatorDynamicFinderMatch` makes it straight forward to test your matching logic. An example using RSpec could look like:

{% highlight ruby %}
describe LegislatorDynamicFinderMatch do
  describe 'find_by_first_name' do
    before do
      @match = LegislatorDynamicFinderMatch.new(:find_by_first_name)
    end
      
    it 'should have attribute :first_name' do
      @match.attribute.should == :first_name
    end
    
    it 'should be a match' do
      @match.should be_a_match
    end
  end
  
  describe 'zomg' do
    before do
      @match = LegislatorDynamicFinderMatch(:zomg)
    end
    
    it 'should have nil attribute' do
      @match.attribute.should be_nil
    end
    
    it 'should not be a match' do
      @match.should_not be_a_match
    end
  end
end
{% endhighlight %}

If you are creating dynamic methods which are just syntactic sugar around another method, like our finder, I think it is sufficient to use mocking to ensure the main method gets called with the correct arguments. Here's an RSpec example:

{% highlight ruby %}
describe Legislator, 'dynamic find_by_first_name' do
  it 'should call find(:first_name => first_name)' do
    Legislator.should_receive(:find).with(:first_name => 'John')
    
    Legislator.find_by_first_name('John')
  end
end
{% endhighlight %}

### Summary

If you're going to use `method_missing`, be sure to implement `respond_to?` in kind.

You can improve the DRYness of these methods by encapsulating the matching logic in a separate class, and you can improve performance by defining methods when they are missing.

