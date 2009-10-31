---
layout: post
title: Using version constants with Jeweler
---

A few weeks back, I quietly released [Jeweler](http://github.com/technicalpickles/jeweler) [1.3.0](http://github.com/technicalpickles/jeweler/tree/v1.3.0). One of the latest additions is the ability to explicitly set a version in your [gemspec](http://docs.rubygems.org/read/chapter/20), rather than using a VERSION file.

There were two motivations for this:

 * Allowing the use of a Version module (ie Jeweler::Version) in your project which contains your version information
  * This lets users of your library be able to check the version of your library they are using
 * Not require an extra file, VERSION, in your project root

One side effect of this is that you can't use the Jeweler rake tasks for bumping the version, since it doesn't know specifically where the version is coming form, and wouldn't know how to regenerate that file with the new version information.

I'll give two examples of using this new functionality. First, we'll just set it directly on the gemspec, and second, we'll use a version constant.

Okay, let's assume you're project is called 'trogdor'. Your `Rakefile` is going to look something like this initially:

{% highlight ruby %}
require 'jeweler'
Jeweler::Tasks.new do |gemspec|
  gemspec.name = 'trogdor'
  # snipped for brevity
end
{% endhighlight %}


All you really need to do is just set `gemspec.version`. We'll use 0.1.0 for an initial release.

{% highlight ruby %}
require 'jeweler'
Jeweler::Tasks.new do |gemspec|
  gemspec.name = 'trogdor'
  gemspec.version = '0.1.0'
  # snipped for brevity
end
{% endhighlight %}

Super easy.

For the second usage, we'll move this version to its own file. Let's make `lib/trogdor/version.rb`:

{% highlight ruby %}
class Trogdor
  module Version
    MAJOR = 0
    MINOR = 1
    PATCH = 0
    STRING = "#{MAJOR}.#{MINOR}.#{PATCH}"
  end
end
{% endhighlight %}

Then we need to update `Rakefile` to require this, and set `gemspec.version` accordingly.

{% highlight ruby %}
require 'jeweler'
require 'lib/trogdor/version'
Jeweler::Tasks.new do |gemspec|
  gemspec.name = 'trogdor'
  gemspec.version = Trogdor::Version::STRING
  # snipped for brevity
end
{% endhighlight %}

Now that we know how to set the version, how does this affect version bumping and releasing? Not much really, except that you can't use the `version:bump` rake tasks. Now you'll do something like this

 * Make awesome code
 * Edit `Rakefile` or `lib/trogdor/version.rb` to reflect the new version
 * Commit `Rakefile` or `lib/trogdor/version.rb`
 * Do the usual `rake release`
 
And that is all there is to it.