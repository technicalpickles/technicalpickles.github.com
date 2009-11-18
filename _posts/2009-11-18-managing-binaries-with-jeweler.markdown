---
layout: post
title: "Managing binaries of your gem with Jeweler"
---
It's pretty useful to inclue a binary with your gems. Maybe it generates something. Or perhaps it's what your gem is all about. Regardless, adding a binary to your gem is almost effortless.

Basically, it plays out like this:

 * Create a `bin` directory
 * Make a ruby script in the `bin` directory (I'll provide an example in a second)
 * Make the ruby script executable
 * Regenerate the gemspec with `rake gemspec`
 * `git add` and `git commit` the executable and gemspec

Notice that we don't actually touch the `Rakefile` to update the gemspec `Jeweler::Tasks`. Under the hood, Jeweler checks the `bin` directory and populates the [executables](http://docs.rubygems.org/read/chapter/20#executables) section of the gemspec accordingly.

## An example binary

Let's use the `jeweler` binary as a template. A few goals I have are:

 * It should be runnable regardless of where ruby is on the path
 * It should be runnable from within the project directory, and NOT have to be installed
 * It should be a good unix citizen and return an appropriate exit code

{% highlight ruby %}
#!/usr/bin/env ruby
$LOAD_PATH.unshift File.join(File.dirname(__FILE__), '..', 'lib')
require 'jeweler/generator'
exit Jeweler::Generator::Application.run!(*ARGV)
{% endhighlight %}

On line 1, we have a shebang. `/usr/bin/env ruby` basically finds any executable in the `PATH`, and executes that.

On line 2, we manipulate the the `LOAD_PATH` so that the lib directory is searched first. This lets us just run `bin/jeweler` and have it load this copy of jeweler, rather than using the installed rubygems

On line 3, we require the code we're going to be using.

Lastly, on line 4, we call the the `run!` method. This takes the arguments passed to the binary, does stuff, and eventually returns an integer. `exit` then, uh, exists, with this as the exit code.

I like this particular approach because it's a lot easier to test a method on class returning something, than it is to stub out `exit`.

## Straying from the defaults

Maybe you don't like there automatically being stuff. Perhaps there's stuff that you that's only for development. Or something super secret. The trick here is to modify `executables` in your `Rakefile`'s `Jeweler::Tasks`. For example, you could go:

{% highlight ruby %}
Jeweler::Tasks.new do |gem|
  # The plebs aren't ready for super-jeweler yet...
  s.executables = ["jeweler"] # note, this is just the file name, not the full path inside of bin
end
{% endhighlight %}
