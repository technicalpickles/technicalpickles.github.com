--- 
title: Of Dreams unfulfilled, JMock, and Expectations
tags: 
- java
- jmock
- testing
layout: post
---
I just had an observation about the pattern of setting up expectations 
of our [jmock](http://www.jmock.org/) tests. So here's a typical code block for setting it up:

    final List<UserGroup> groups = this.groups;
    final List<User> users = this.users;
    final User user = users.get(0);

    final UserService userService = this.userService;
    this.jmockCtx.checking(new Expectations()
    {{
      // first, rehydrate
      allowing(userService).findAllGroups();
      will(returnValue(groups));
      allowing(userService).findAllUsers();
      will(returnValue(users));
      will(returnValue(true));
      // now, save
      one(userService).createUser(user);
      one(userService).findAllUsers();
      will(returnValue(users));
    }});

I was thinking, hey, why not just use this.groups, this.users, etc, 
directly in the expectations, instead of making new references to them? It might look like:

    this.jmockCtx.checking(new Expectations()
    {{
      // first, rehydrate
      allowing(this.userService).findAllGroups();
      ...
    }});

This ends up being an error because 'userService' cannot be resolved or 
is not a field. Unexpected behavior, right? Almost.

A little background about what this code chunk is actually doing: when 
we do new Expectations() {}, we're actually creating an anonymous 
subclass. At that point, 'this' would refer to the Expecations subclass, 
not the test we're in.

When we do the second set of {}s, we're adding an initializer block. The 
best way of thinking about this is that basically, it gets invoked when 
you instantiate an instance of an object, similar, but different to the 
normal contstructor. You may be more familiar with static initializer 
blocks, doing something like:

    public class Something {
      private static Map map = new HashMap();
      static {
        map.put("key","value");
      }
      ...
    }

So my dream of using this.users and pals in my Expecations, can only 
be that, a dream.
