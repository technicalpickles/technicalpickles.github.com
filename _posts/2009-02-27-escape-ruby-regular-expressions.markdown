---
layout: post
title: Escape Ruby Regular Expressions
---
If you're doing any work with [cucumber](http://cukes.info/), it would do you well to become more familiar with Ruby regular expressions.

One useful piece of regular expression functionality is that you can interpolate variables inside an regular expression just like you would in a string. In cucumber, we could write a [step](http://wiki.github.com/aslakhellesoy/cucumber/step-definitions) like this:

{% highlight ruby %}
Then /^I should see "(.*)"$/ do |text|
  assert_match /#{text}/, @response.body
end
{% endhighlight %}
    
There is a problem though. If `text = 'Push Push (Lady Lightning)` and `@response_body = 'Push Push (Lady Lightning)`, the assertion would fail!

This is because there are some characters that have special meaning to regular expressions. Here, parentheses indicate [capture groups](http://www.regular-expressions.info/brackets.html) which lets you extracted matched values. The net effect is the expression would match 'Push Push Lady Lightning' and capture "Lady Lighning", but would not match 'Push Push (Lady Lightning)'.

You can get around this by escaping characters with `\`  easily enough if we were doing it by hand. That won't do for since we have a variable though. Fortunately, there is method [`Regexp.escape`](http://www.ruby-doc.org/core/classes/Regexp.html#M001216) which takes a string and escapes any special characters.

We can update our step to look like:

{% highlight ruby %}
Then /^I should see "(.*)"$/ do |text|
  assert_match /#{Regexp.escape(text)}/, @response.body
end
{% endhighlight %}

This assertion will now be successful if `text = 'Push Push (Lady Lightning)` and `@response_body = 'Push Push (Lady Lightning)` as you'd expected.
