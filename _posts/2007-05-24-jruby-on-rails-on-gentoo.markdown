--- 
permalink: /posts/jruby-on-rails-on-gentoo
title: JRuby on Rails on Gentoo
tags: 
- gentoo
- jruby
- rails
- ruby
layout: post
---
<p>After adding <a href="http://jruby.codehaus.org">JRuby</a> 1.0.0 RC2 to the tree, I began poking around using it to run a little blog I've been writing in rails. Surprisingly, the process was quite easy. I just had to package the JDBC wrapper for ActiveRecord.</p>

<p>So here are the details, assuming you are using mysql:</p>

<blockquote><pre># emerge &gt;=jruby-1.0.1_rc2-r1 activerecord-jdbc jdbc-mysql</pre><p></p></blockquote>

<p>I added quite a few changes to -r1, including the ability to use gems and other ruby packages already installed on the system. To do this, there is now a symlink from /<em>usr/share/jruby/lib/ruby/gems</em> to <em>/usr/lib/ruby/gems</em>, and the same for site_ruby as well. If you had an earlier version of jruby, they used to be a separate directories, where you'd have to install gems again. If that's the case, you'll see a warnings, and eventually an error that you need to move /usr/share/jruby/lib/ruby/gems and <em>/usr/share/jruby/lib/ruby/site_ruby</em> out of the way before you can continue.</p>

<p>After things have successfully merged, we are ready to add the appropriate configuration. I'm going to assume here that you have already have a rails app to work on, and using mysql for a database. If not, there may be a trillion or so places on the interwebs that explain how to get going.</p>

<p>First, we add need to add a bit to the <em>config/environment.rb</em>:</p>

<blockquote><pre>def using_jruby?
  RUBY_PLATFORM =~ /java/
end

if using_jruby? 
  require 'rubygems'
  RAILS_CONNECTION_ADAPTERS = %w(jdbc)
end</pre>
<p></p></blockquote><br>
This can be placed after the <em>require File.join(File.dirname(__FILE__), 'boot')</em> line. If there's a better place <em>using_jruby?</em> should be defined, please let me know.

<p>Now we wander over to <em>config/database.yml</em>. Here is what was originally there:</p>

<blockquote><pre>development:
  adapter: mysql
  socket: /var/run/mysqld/mysqld.sock
  database: yourapp_development
  username: root
  password:</pre><p></p></blockquote>

<p>Now we update it to use:
</p><blockquote><pre>development:
  &lt;% if using_jruby? %&gt;
  adapter: jdbc
  driver: com.mysql.jdbc.Driver
  url: jdbc:mysql://localhost/yourapp_development
  &lt;% else  %&gt;
  adapter: mysql
  socket: /var/run/mysqld/mysqld.sock
  &lt;% end %&gt;
  database: yourapp_development
  username: root
  password:</pre><p></p></blockquote>

<p>So, if we're using jruby, we use the JDBC adapter, with the appropriate driver, and point it at the right URL. Otherwise, it'll use what we had before.</p>

<p>You'll likely also want to apply this to the test and/or production databases.</p>

<p>One last thing we need to do is make sure that the jruby gets jdbc-mysql on the classpath. The simplistic way to do this would be to just:</p>

<blockquote><pre>$ export CLASSPATH=$(java-config --classpath jdbc-mysql)</pre><p></p></blockquote>

<p>That works for testing, but I want something a little more permanent. So now, we use a relatively undocumented feature of java-config.</p>

<blockquote><pre>$ mkdir -p ~/.gentoo/java-config-2/launcher.d/
echo CLASSPATH=\$(java-config --classpath jdbc-mysql) \
&gt; ~/.gentoo/java-config-2/launcher.d/jruby</pre><p></p></blockquote>

<p>/usr/bin/jruby, and most Java applications on Gentoo use a launcher utility we wrote, to help with building classpaths, invoking the right class, and so on. What this snippet did was create some custom configuration for the jruby launcher, which would get used whenever you invoke jruby. In this case, we add jdbc-mysql to the classpath.</p>

<p>Now we should be all ready to actually use it.</p>

<blockquote><pre>$ jruby script/server</pre><p></p></blockquote>

<p>You should now be able to hit your application at the usual location. Keep in mind that unless you invoke Note: you'll always have to invoke it with jruby, or alternatively, you can update the #! line in script/server to be "#!/usr/bin/env jruby".</p>

<p>One issue I have run into is that things freak out when running unit tests, because it "can't find an appropriate driver", which doesn't make sense when it works fine for development. Here's a snippet:</p>

<blockquote><pre>$ jruby /usr/bin/rake  test
-----SNIP-----
1) Error:
test_create(BlogPostsControllerTest):
RuntimeError: The driver encountered an error: java.sql.SQLException: No suitable driver
    /usr/share/jruby/lib/ruby/gems/1.8/gems/ActiveRecord-JDBC-0.3.1/lib/active_record/connection_adapters/jdbc_adapter.rb:208:in `initialize'
    /usr/share/jruby/lib/ruby/gems/1.8/gems/ActiveRecord-JDBC-0.3.1/lib/active_record/connection_adapters/jdbc_adapter.rb:10:in `new'
    /usr/share/jruby/lib/ruby/gems/1.8/gems/ActiveRecord-JDBC-0.3.1/lib/active_record/connection_adapters/jdbc_adapter.rb:10:in `jdbc_connection'
    /usr/share/jruby/lib/ruby/gems/1.8/gems/activerecord-1.15.3/lib/active_record/connection_adapters/abstract/connection_specification.rb:262:in `send'
    /usr/share/jruby/lib/ruby/gems/1.8/gems/activerecord-1.15.3/lib/active_record/connection_adapters/abstract/connection_specification.rb:262:in `connection='
    /usr/share/jruby/lib/ruby/gems/1.8/gems/activerecord-1.15.3/lib/active_record/query_cache.rb:54:in `connection='
    /usr/share/jruby/lib/ruby/gems/1.8/gems/activerecord-1.15.3/lib/active_record/connection_adapters/abstract/connection_specification.rb:230:in `retrieve_connection'
    /usr/share/jruby/lib/ruby/gems/1.8/gems/activerecord-1.15.3/lib/active_record/connection_adapters/abstract/connection_specification.rb:78:in `connection'
    /usr/share/jruby/lib/ruby/gems/1.8/gems/activerecord-1.15.3/lib/active_record/fixtures.rb:247:in `create_fixtures'
    /usr/share/jruby/lib/ruby/gems/1.8/gems/activerecord-1.15.3/lib/active_record/fixtures.rb:593:in `load_fixtures'
    /usr/share/jruby/lib/ruby/gems/1.8/gems/activerecord-1.15.3/lib/active_record/fixtures.rb:538:in `setup_with_fixtures'
    /usr/share/jruby/lib/ruby/gems/1.8/gems/activerecord-1.15.3/lib/active_record/fixtures.rb:575:in `setup'
    ./test/functional/blog_posts_controller_test.rb:17:in `setup'
-----SNIP-----
</pre><p></p></blockquote>

<p>Lastly, here are some resources that may be useful:
</p><ul>
  <li><a href="http://www.headius.com/jrubywiki/index.php/Main_Page">http://www.headius.com/jrubywiki/index.php/Main_Page</a></li>
  <li><a href="http://www.headius.com/jrubywiki/index.php/JRuby_on_Rails">http://www.headius.com/jrubywiki/index.php/JRuby_on_Rails</a></li>
  <li><a href="http://www.headius.com/jrubywiki/index.php/Running_Rails_with_ActiveRecord-JDBC">http://www.headius.com/jrubywiki/index.php/Running_Rails_with_ActiveRecord-JDBC</a></li></ul>					