--- 
permalink: /posts/switched-templating-engine-to-bluecloth
title: Switched templating engine to BlueCloth
tags: 
- bluecloth
- rails
- redcloth
- tinymce
- ultraviolet
layout: post
---
Originally, I had implemented the main data entry using [TinyMCE](http://tinymce.moxiecode.com/). TinyMCE itself is a pretty cool piece of software. I mean, it gives you a full WYSIWYG (mostly) form, and the output is actual HTML. But in the end, it felt like a bit too much

So, the issues I had were:
 * It was more like WYSISWYG (what you see is sort of what you get)
 * Icky markup
 * A bit of overhead from the extra javascript files loading
 * Not any way to do code highlighting. This was the breaking point, since I plan on having lots of these

After deciding to ditch TinyMCE, the next step was to figure out what to replace it with. There are a few out there for rails:

 * [BlueCloth](http://www.deveiate.org/projects/BlueCloth), an engine which uses the [Markdown](http://daringfireball.net/projects/markdown) syntax
 * [RedCloth](http://whytheluckystiff.net/ruby/redcloth/), which uses [Textile](http://hobix.com/textile/)

Sometimes, I hate how developers name stuff :) Here's a break down of much comparison:

 * Pretty equivalent as far as support/ease of use in rails goes.
 * Both support straight-up HTML, so old posts will still work, and you can also drop down to HTML when a particular tricky display issue comes up.
 * Markdown seems a bit more straight forward syntax, but I suspect it's because it's very similar to the syntax used for [Trac](http://trac.edgewall.org/)'s syntax, which I'm particularly fond of

So I went with BlueCloth.

Changes to my code were pretty trivial. I added this to my BlogPost model:

          def to_html
            BlueCloth.new(self.body).to_html
          end

And in my view, I changed the following bit:

          <div class="blogbody">
            <%= blog_post.body %>
          </div>

to...

          <div class="blogbody">
            <%= blog_post.to_html %>
          </div>

Easy peezy.

Now, I can use nifty code snippets as you can see. Unfortunately, it's not quite as nifty as it could be, as it, it could use some nice syntax highlighting.

My idea is to use [Ultraviolet](http://ultraviolet.rubyforge.org/) to do that. So, I could add bits to `BlogPost#to_html`, to parse out the contents of `<code>` blocks, run it through Ultraviolet, and return that. I'll save that for another day though.