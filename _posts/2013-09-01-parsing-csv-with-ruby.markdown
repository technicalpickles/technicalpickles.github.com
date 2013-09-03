---
title: Parsing CSV with Ruby
layout: post
tags: ruby,csv
---

I'm filing this one under "blog posts I wish existed when I was googling." If you are dealing with data on the web, you are probably most familiar with JSON and XML. Less common nowadays is CSV, but if it's all you have, and the alternative is screen scraping, then you are thankful.

![death by snoo snoo](http://mlkshk.com/r/5NX8.gif)

Imagine we have some data in CSV:

%{highlight csv}
Year,Make,Model,Description,Price
1997,Ford,E350,"ac, abs, moon",3000.00
1999,Chevy,"Venture ""Extended Edition""","",4900.00
1999,Chevy,"Venture ""Extended Edition, Very Large""",,5000.00
1996,Jeep,Grand Cherokee,"MUST SELL!
air, moon roof, loaded",4799.00
%{endhighlight}

Now as a Ruby developer, particularly that has been infected by Rails, you'd be able to imagine this as an array of hashes, with keys/values using the column header, as the keys symbolized, and the values converted to numerics and blank ones converted to nil:

```ruby
[
  {:year => 1997, :make => 'Ford', :model => 'E350', :description => 'ac, abs, moon', :price => 3000.00},
  {:year => 1999, :make => 'Chevy', :model => 'Venture "Extended Edition"', :description => nil, :price => 4900.00},
  {:year => 1999, :make => 'Chevy', :model => 'Venture "Extended Edition, Very Large"', :description => nil, :price => 5000.00},
  {:year => 1996, :make => 'Jeep', :model => 'Grand Cherokee', :description => "MUST SELL!\nair, moon roof, loaded", :price => 4799.00}
]
```

It might be tempting to just use regular expressions or read each line and `split(',')`, but there are many nuances to the CSV format. Ruby's stdlib [includes CSV support](http://ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV.html) to help us realize this dream with minimal hassle.

The documentation is unclear on the differences between methods of opening a CSV. In particular, there is a `#parse` and `#new`, the `#parse` only returns an array of arrays for data, but #new` is more flexible as we'll see. Nor does it help realize this Ruby data structure easily. We'll be walking through the discovery of this from the documentation, but if you want the final solution [jump down here](#final-solution)

A closer look at [#new](http://ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV.html#method-c-new) shows that it can take either a String or an IO-like object. The latter is interesting if you've gotten uploaded data (ie [ActionPack's UploadedData](http://api.rubyonrails.org/classes/ActionDispatch/Http/UploadedFile.html)):

%{highlight ruby}
csv = CSV.new(body)
%{endhighlight}

With this csv loaded, you can use `#to_a` to get the array of data on it. This is a convenience method for [#read](http://ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV.html#method-i-read) and [#readlines](http://ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV.html#method-i-readlines). This reads the remaining data in the string or IO you passed in, so any subsequent calls ends up returning an empty array.

%{highlight ruby}
csv.to_a
# => [["Year", "Make", "Model", "Description", "Price"], ["1997", "Ford", "E350", "ac, abs, moon", "3000.00"], ["1999", "Chevy", "Venture \"Extended Edition\"", "", "4900.00"], ["1999", "Chevy", "Venture \"Extended Edition, Very Large\"", nil, "5000.00"], ["1996", "Jeep", "Grand Cherokee", "MUST SELL!\nair, moon roof, loaded", "4799.00"]]
csv.to_a
# => []
%{endhighlight}

This gives us an array of arrays, and the first element is an array with the headers. We are further than we started, but we still don't have an array of hashes.

One of new [#new](http://ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV.html#method-i-readlines)'s options is `:headers`, which basically does just that:

%{highlight ruby}
csv.to_a
#=> [#<CSV::Row "Year":"1997" "Make":"Ford" "Model":"E350" "Description":"ac, abs, moon" "Price":"3000.00">, #<CSV::Row "Year":"1999" "Make":"Chevy" "Model":"Venture \"Extended Edition\"" "Description":"" "Price":"4900.00">, #<CSV::Row "Year":"1999" "Make":"Chevy" "Model":"Venture \"Extended Edition, Very Large\"" "Description":nil "Price":"5000.00">, #<CSV::Row "Year":"1996" "Make":"Jeep" "Model":"Grand Cherokee" "Description":"MUST SELL!\nair, moon roof, loaded" "Price":"4799.00">]
%{endhighlight}

Actually, this looks like an array of [CSV::Row](http://ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV/Row.html)s. It has a [#to_hash](http://ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV/Row.html#method-i-to_hash). We can use `map` to apply that to each element in the array:

%{highlight ruby}
csv.to_a.map {|row| row.to_hash }
# => [{"Year"=>"1997", "Make"=>"Ford", "Model"=>"E350", "Description"=>"ac, abs, moon", "Price"=>"3000.00"}, {"Year"=>"1999", "Make"=>"Chevy", "Model"=>"Venture \"Extended Edition\"", "Description"=>"", "Price"=>"4900.00"}, {"Year"=>"1999", "Make"=>"Chevy", "Model"=>"Venture \"Extended Edition, Very Large\"", "Description"=>nil, "Price"=>"5000.00"}, {"Year"=>"1996", "Make"=>"Jeep", "Model"=>"Grand Cherokee", "Description"=>"MUST SELL!\nair, moon roof, loaded", "Price"=>"4799.00"}]
%{endhighlight}

We are getting warmer! It's an array of hashes, but the keys are literally the values from the header row as Strings. Another peak back at [#new](http://ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV.html#method-c-new) shows a `:header_converters` for converting the headers from their raw values. It's not easy to find on the page, but [here is a list of all header converters](http://www.ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV.html#HeaderConverters), with `:symbol` being the one we care about.

%{highlight ruby}
csv = CSV.new(body, :headers => true, :header_converters => :symbol)
csv.to_a.map {|row| row.to_hash }
# => [{:year=>"1997", :make=>"Ford", :model=>"E350", :description=>"ac, abs, moon", :price=>"3000.00"}, {:year=>"1999", :make=>"Chevy", :model=>"Venture \"Extended Edition\"", :description=>"", :price=>"4900.00"}, {:year=>"1999", :make=>"Chevy", :model=>"Venture \"Extended Edition, Very Large\"", :description=>nil, :price=>"5000.00"}, {:year=>"1996", :make=>"Jeep", :model=>"Grand Cherokee", :description=>"MUST SELL!\nair, moon roof, loaded", :price=>"4799.00"}]
%{endhighlight}

Even warmer! The hash keys are now symoblized, but we still have some wonky data in there. There's blank strings (""), and numerics as strings ("1999", "4900.00"). Yet another option to [#new](http://ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV.html#method-c-new) is `:converts`, to convert each row's values. [Here is a list of available converts](http://www.ruby-doc.org/stdlib-2.0/libdoc/csv/rdoc/CSV.html#Converters), but we want `:all` which converts numerics (float and intergers) and date & datetimes:

%{highlight ruby}
csv = CSV.new(body, :headers => true, :header_converters => :symbol, :converters => :all)
csv.to_a.map {|row| row.to_hash }
#=> [{:year=>1997, :make=>"Ford", :model=>"E350", :description=>"ac, abs, moon", :price=>3000.0}, {:year=>1999, :make=>"Chevy", :model=>"Venture \"Extended Edition\"", :description=>"", :price=>4900.0}, {:year=>1999, :make=>"Chevy", :model=>"Venture \"Extended Edition, Very Large\"", :description=>nil, :price=>5000.0}, {:year=>1996, :make=>"Jeep", :model=>"Grand Cherokee", :description=>"MUST SELL!\nair, moon roof, loaded", :price=>4799.0}]
%{endhighlight}

SO WARM! The only thing I can complain about is that there's blank strings (""). While there's nothing else built in to help us, there is support for adding your own custom converters. You can add your own converts to `CSV::Converters` and `CSV::HeaderConverters` as you want. These are just hashes, with the key being the name, and the value being a lambda that takes  a string and should return the converted value. You can then pass an array of converters to use to `#new`.

<a name="final-solution"></a>

%{highlight ruby}
CSV::Converters[:blank_to_nil] = lambda do |field|
  field && field.empty? ? nil : field
end
csv = CSV.new(body, :headers => true, :header_converters => :symbol, :converters => [:all, :blank_to_nil])
csv.to_a.map {|row| row.to_hash }
# => [{:year=>1997, :make=>"Ford", :model=>"E350", :description=>"ac, abs, moon", :price=>3000.0}, {:year=>1999, :make=>"Chevy", :model=>"Venture \"Extended Edition\"", :description=>nil, :price=>4900.0}, {:year=>1999, :make=>"Chevy", :model=>"Venture \"Extended Edition, Very Large\"", :description=>nil, :price=>5000.0}, {:year=>1996, :make=>"Jeep", :model=>"Grand Cherokee", :description=>"MUST SELL!\nair, moon roof, loaded", :price=>4799.0}]
%{endhighlight}

HOT HOT HOT. That is basically exactly what I set out to do, so we are all done here.
