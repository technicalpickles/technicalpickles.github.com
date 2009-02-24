--- 
permalink: /posts/stop-net-http-dead-in-its-tracks-with-fakeweb.html
title: Stop Net::HTTP dead in its tracks with fakeweb
tags: 
- net::http
- ruby
- shoulda
- testing
- yaml
layout: post
---
It's a well known fact that you should be [testing all the time](http://whendoitest.com/). You know you should be stubbing out external network traffic too, but sometimes it's easy to forget. The consequences of not stubbing out network connections are that your test suite becomes slow, you are forced to be tethered to a network, and you generally find yourself in a dark, dark place.

Enter [fakeweb](http://github.com/chrisk/fakeweb): it stops `Net::HTTP` dead. To get started, add this to your `test_helper.rb`:

{% highlight ruby %}
require 'fakeweb'
FakeWeb.allow_net_connect = false
{% endhighlight %}

This loads fakeweb, and then prevents Net::HTTP from actually hitting the network. If you have any tests that use the Net::HTTP, you are going to see a lot of tests errored out. Here's an example backtrace:

    FakeWeb::NetConnectNotAllowedError: Real HTTP connections are disabled. Unregistered URI: http://github.com:80/api/v1/json/defunkt?
        /opt/ruby-enterprise-1.8.6-20080810/lib/ruby/gems/1.8/gems/chrisk-fakeweb-1.1.2.6/lib/fake_net_http.rb:64:in `request'
        /opt/ruby-enterprise-1.8.6-20080810/lib/ruby/gems/1.8/gems/httparty-0.1.3/lib/httparty.rb:127:in `send_request'
        /opt/ruby-enterprise-1.8.6-20080810/lib/ruby/gems/1.8/gems/httparty-0.1.3/lib/httparty.rb:73:in `get'
        ./test/../lib/github-party.rb:10:in `user'

At this point, you use `FakeWeb.register_uri` to record a URI and response. I found it best to use `curl` to capture the response, save it to a fixture, and then load it during testing.

    $ mkdir -p test/fixtures
    $ curl -is http://github.com/api/v1/json/defunkt > test/fixtures/user.json

Now we can use the saved response:

{% highlight ruby %}
class GithubPartyTest < Test::Unit::TestCase
  context "user lookup" do
    setup do
      FakeWeb.register_uri('http://github.com/api/v1/json/defunkt', :response => File.join(File.dirname(__FILE__), 'fixtures', 'user.json'))
      @user = GitHubParty.user('defunkt')
    end
    # tests go here
  end
end
{% endhighlight %}
    
Not only does this pass, but it runs faster and you can take it offline at your discretion.

For my example, my code is using Net::HTTP, so it makes sense to use fakeweb to register allowed URIs. It gets more complicated if you are using a library which is making the Net::HTTP calls. Imagine something like [flickr\_fu](http://github.com/commonthread/flickr_fu/tree/master) or [youtube-g](http://github.com/tmm1/youtube-g/tree/master). Registering the precise URIs the library uses would break encapsulation, not to mention being annoying and tedious.

I found a better approach is to capture the result of the library call to YAML and then use a mocking library, such as [mocha](http://mocha.rubyforge.org/), to return the deserialized YAML. [Dan Croak](http://dancroak.tumblr.com/) [wrote about this approach](http://giantrobots.thoughtbot.com/2008/12/23/script-console-tips) a few weeks ago, but now we add fakeweb to the mix.

To capture the output, open `script/console` or `irb`:

    >> videos = client.videos_by(:most_viewed, :query => 'Bruce Springsteen')
    >> FileUtil.mkdir_p 'test/fixtures/videos'
    >> File.open('test/fixtures/videos/bruce_springsteen.yml', 'w') {|f| f.write videos.to_yaml}
    
To facilitate using this fixture, we can make a small helper in `test_helper.rb`:

{% highlight ruby %}
def load_yaml_fixture(path)
  adjusted_path = File.join File.dirname(__FILE__), "fixtures", path
  YAML::load_file adjusted_path
end
{% endhighlight %}
    
We can now reply the captured results using this helper and mocha:

{% highlight ruby %}
class VideoTest < ActiveSupport::TestCase
  context "Video.refresh_for!(@topic)" do
    setup do
      @topic  = Factory(:topic, :name => "Bruce Springsteen")
      @videos = load_yaml_fixture "you_tube/bruce_springsteen.yml"
      YouTubeClient.stubs(:search).with(@topic.name).returns(@videos)
      Video.refresh_for! @topic
    end
    # tests go here
  end
end
{% endhighlight %}
    
Using fakeweb and these two methods, we should be all set for preventing any outgoing Net::HTTP connections. Be wary, though. Your tests will be faster and let you go offline, but they are just snapshots of a HTTP request or library call, so may change over time.
