--- 
permalink: /posts/our-evaluation-of-selenium-for-web-testing
title: Our evaluation of Selenium for web testing
tags: 
- ant
- bamboo
- java
- junit
- selenium
- testing
- web development
- web testing
layout: post
---
Like the good little code monkeys we are, it is time for us to adopt some form of web testing. There is only so much unit and integration testing can help you, before you need a fuller solution.

Given its maturity, [Selenium](http://www.openqa.org/selenium/) seemed like the obvious choice. Here's what I found:

### The Good

Here's some things we liked:

  * An IDE, as a Firefox Extension, for building unit tests
  * Support for testing in a many languages, such as Java, Ruby, their own Selenese (basically glorified HTML)
  * The IDE generates Selenese, which can easily be converted to one of the above mentioned languages
  * Support for running in multiple browsers across multiple platforms
  * Support for running selenese tests using ant

All in all, pretty cool stuff.

### The Bad

However, when we went down to our particulars for our project, things fell a little short. As a brief background, we use ant for building, junit for testing, and bamboo for continuous testing.

You can convert from Selenese to your language choice, but once you do, you can't go back. This is kind of expected, as you can do all sorts of trickery to the code after converting it from Selenese. The API for interacting with Selenium closely follows the Selenese. Here's an example of Selenese, and then converted Java.

    <tr>
       <td>open</td>
       <td>/</td>
       <td></td>
    </tr>
    <tr>
       <td>clickAndWait</td>
       <td>link=diabetic nephropathy, schizophrenia, and bipolar disorder</td>
       <td></td>
    </tr>
    <tr>
       <td>clickAndWait</td>
       <td>link=Steering Committee</td>
       <td></td>
    </tr> 

    public class NewTest extends SeleneseTestCase {
       public void testNew() throws Exception {
           selenium.open("/");
           selenium.click("link=diabetic nephropathy, schizophrenia, and bipolar disorder");
           selenium.waitForPageToLoad("30000");
           selenium.click("link=Steering Committee");
           selenium.waitForPageToLoad("30000");
       }
    } 

The code here produced isn't the cleanest, because the API is closely following Selenium conventions, rather than using the language's conventions. For example, Maps should be used, instead of something like "link=Steering Committee", and Integers should be used instead of number in a String.

Because you can't go back to Selenese from code, we decided we should always work with Selenese, so we can maintain tests easily using the IDE. This means we need to use the 'selenese' ant tasks for running our tests.

This is how our invokation looks:

    <selenese suite="${dir.webtest}/selenium_test_suite.html" 
      results="${dir.selenium}/selenium_test_results.html"
      browser="*firefox"
      timeoutInSeconds="90"
      startURL="${webapp.url}/" />		

The first issue is that you have to specify a test suite. You can't just give it a directory, or fileset, that includes all your tests. This is one extra thing to maintain, and it becomes easy to forget to add new tests to it.

The other issue is the output. It's just an HTML. We want to be able to generate junit-like XML, so a report can be generated along with all our other tests.

### Coming to terms

Since this is open source, the obvious solution is to get hacking, and so I started delving into the source tree.

I had two primary impressions of the codebase.  The first is that there aren't suitable layers of abstraction. For example, instead of having a class representing a suite of tests, and a class for representing tests, java.io.File instances get passed around and grokked as their needed. Second was that it isn't sufficiently extensible for. There weren't any places we could hook in, or classes to extend, to change the results ouput, or even get at it.

#### On-the-fly suites

The issue here is that the code is expecting a File to be passed in, which contains all the tests. I had hoped it had some abstraction which would parse through the suite, so if you wanted, you could programattically build up a suite.

So here, we can get around this by building up a temporary File, which contains the appropriate HTML for Selenium to understand it as a suite. 

#### JUnit reports

When looking at the code, I had hoped to see like a TestResult class, that you could get the results out of, and then have a way for outputting the results as you see fit.

Since we can't do that, the next best thing would be to parse through the results' HTML, and spit out XML laid out in a way that JUnit's reporting mechanism would be able to understand.

### Success!

After a day or two of hacking around, I was able to implement the workarounds I mentioned here.

Unfortunately, the implementations turned out to be a bit too closely tied our environment, so I can't readily turn it out to the rest of the world.

Hopefully, I will have the time to do the necessary refactorings to be able to do so.
