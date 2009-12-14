---
layout: post
---

If you think about it, programming is really about taking some input from a user, and displaying some output back to them. When we're down at the command line, this usually means reading from the standard input and writing to the standard output.

In ruby, you'll see code that interacts with standard in and standard out like so:

{% highlight ruby %}
class MyApp
  def prompt_user 
    user_selection = gets.chomp             # => 'ponies'
    puts "User selected: #{user_selection}" # Displays User selected: ponies
    user_selection
  end
end
{% endhighlight %}
    

Sadly though, when it comes testing, usually the input/output stuff is glossed over. Why though?

 * Extraneous output is often generated during testing
 * It's hard to specify what stdin returns and verify what stdout displays back to the user

"Don't let not having the tools be your trepidation... I will never make a criticism if I don't have a resolution." - [Joel Bauer](http://www.youtube.com/watch?v=4YBxeDN4tbk)

Allow me, then, to outline a pattern for using standard input and standard output in your Ruby code in a way that's testable.

First off, don't use just 'puts' and 'gets' by themselves. If you check out the [rdoc for `Kernel`](http://ruby-doc.org/core/classes/Kernel.html#M005954), you'll see that these are just shorthand for `$stdout.puts` and `$stdin.gets`. So we'll change our code to be more like:

{% highlight ruby %}
class MyApp
  def prompt_user 
    user_selection = $stdin.gets.chomp              # => 'ponies'
    $stdout.puts "User selected: #{user_selection}" # Displays User selected: ponies
    user_selection
  end
end
{% endhighlight %}

Tests still pass, right? Alright, next we're going to create some `attr_accessor`s on `MyApp` for the input/output. By default, we'll make them point at `$stdin` and `$stdout` so they'll work as expected by default, but during your tests... well, we'll get to that in a minute.

{% highlight ruby %}
class MyApp
  attr_accessor :input, :output

  def initialize
    self.input  = $stdin
    self.output = $stdout
  end

  def prompt_user 
    user_selection = input.gets.chomp              # => 'ponies'
    output.puts "User selected: #{user_selection}" # Displays User selected: ponies
    user_selection
  end
end
{% endhighlight %}

Now we're setup to test this proper like.

But first, we need a little background on `$stdin` and `$stdout`. If you pop up an `irb` session and enter `$stdout.class`, you'll see its is [IO](http://ruby-doc.org/core/classes/IO.html). I'm not going to go into much more detail, but suffice to say, [StringIO](http://ruby-doc.org/core/classes/StringIO.html) exists as a way to make an `IO` that you can muck around with more easily for testing. To provide input to `StringIO`, you pass a string to its constructor. To check the output to `StringIO`, use the `string` method to return it. Knowing that, we can test it now:

{% highlight ruby %}
describe MyApp, "prompt_user"
  before do
    @my_app = MyApp.new

    @input  = StringIO.new("ponies\n")
    @output = StringIO.new

    @my_app.input = @input
    @my_app.output = @output

    @user_selection = @my_app.prompt_user
  end

  it "should output the user's input" do
    @output.string.should =~ /ponies/
  end

  it "should return the user's selection" do
    @user_selection.should == "ponies"
  end
end
{% endhighlight %}

And there we go: ruby code using stdin and stdout that we're able to test easily.
