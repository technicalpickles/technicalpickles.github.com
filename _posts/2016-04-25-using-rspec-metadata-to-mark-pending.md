title: Using RSpec metadata to mark specs as pending
layout: post
tags: ruby,rspec
---


```ruby
config.before :each, :pending => true do
  pending
end
```
