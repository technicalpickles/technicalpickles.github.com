--- 
title: Haml be blowing my spot
tags: 
- haml
- rails
- ruby
layout: default
---
I've heard of the awesome that is [Haml](http://haml.hamptoncatlin.com/) before. I've had friends rave about it. But it was only recently that I broke down to actually use it.

So I went forth and migrated my blog's rhtml templates to haml. Hell, I even migrated my [resume](/resume) from straight-up html to Haml.

It's good. Really good. But, I have some regrets.

One of the features of haml is to automatically pretty-print your markup. But, can you think of any elements that would probably be better left un-pretty-printified?

 * `<pre>`
 * `<textarea>`

So, if you're wondering why my code snippets are all skewed, you have your answer.

I haven't found a good work around yet. An obvious answer would be using filters, but this doesn't quite work since I have markup stored in my model. There seems to be a `:dirty` option for Haml which doesn't format things, but I haven't quite found out how to flip that switch.

So the search continues.