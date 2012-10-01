--- 
title: Focusing a form field at page load with MochiKit
tags: 
- forms
- html
- javascript
- mochikit
layout: post
---
As far as I can tell, the only way to make a form field be focused by default at page load is by using Javascript.

Code snippets you find online will typically do something like:

<pre><code class="html">&lt;html&gt;
  &lt;head&gt;
    ...
  &lt;/head&gt;
  &lt;body onload=&quot;document.someform.somefield.focus()&quot;&gt;
    ...
  &lt;/body&gt;
&lt;/html&gt;</code></pre>

That's nice and all, but this can be problematic when you have other onload methods for your body tag.

[MochiKit](http://www.mochikit.com/) has some nice facilities for handling events like this, what it calls [MochiKit.Signal](http://www.mochikit.com/doc/html/MochiKit/Signal.html). It allows you to connect however many functions to a particular event as you need.

Here's how I did it:

<pre><code class="html">&lt;html&gt;
  &lt;head&gt;
    ...
    &lt;script type=&quot;text/javascript&quot; src=&quot;/your/path/to/MochiKit.js&quot;&gt;&lt;/script&gt;
    &lt;script type=&quot;text/javascript&quot;&gt;
      var focusInitialInput = function() {
        document.someform.somefield.focus();
      }
      connect(window, &#39;onload&#39;, focusInitialInput);
    &lt;/script&gt;
  &lt;/head&gt;
  &lt;body&gt;
    ...
  &lt;/body&gt;
&lt;/html&gt;</code></pre>
  
Pretty simple. This is only scratching the surface of MochiKit's Signal handling.
