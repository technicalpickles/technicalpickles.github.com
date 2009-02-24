--- 
permalink: /posts/authentication-and-authorization-remembering-were-you-came-from.html
title: "Authentication and authorization: Remembering were you came from"
tags: 
- authentication
- authorization
- rails
- users
layout: post
---
Most webapps need authorization and authentication of some sort, right?

If you happen to have a copy of [Agile Web Development with Rails](http://www.pragmaticprogrammer.com/titles/rails2/index.html), then you are fortunate, as it covers adding support for that very thing.

It is a bit simplistic though. Books have to have a finite length, and so some not everything can be covered in super depth.

I followed their recipe for authentication on my blog, and my annoyance was that if you request an authorized action, you get prompted to login, but after logging in, you get sent to a single page you set in code, NOT the resource you were initially requesting.

Fortunately, this is pretty simple to address.

So if you were following things in the book, in ApplicationController, you have something like:

    def authorize
      unless User.find_by_id(session[:user_id])
        flash[:notice] = "Please log in"
        redirect_to(:controller => "login" , :action => "login" )
      end
    end

So the trick here is to record what was being requested in the session. The URI being requested is available from [request's](http://api.rubyonrails.org/classes/ActionController/AbstractRequest.html) [request_uri](http://api.rubyonrails.org/classes/ActionController/AbstractRequest.html#M000239) method. The updated authorize looks like:


    def authorize
      unless User.find_by_id(session[:user_id])
        flash[:notice] = "Please log in"
		session[:request_uri] = request.request_uri
        redirect_to(:controller => "login" , :action => "login" )
      end
    end

We remember what the URI requested was, and get sent to the login page in UserController. The trick is to do something useful with it in the login action. Initially, login will look like:

    def login
      session[:user_id] = nil
      if request.post?
        user = User.authenticate(params[:name], params[:password])
        if user
          session[:user_id] = user.id
          redirect_to(:action => "index" )
        else
          flash[:notice] = "Invalid user/password combination"
        end
      end
    end

If the request URI is stashed on the session, we want to redirect it and reset the value, otherwise, just redirect to the default page we had before. Here's the resulting code:

    def login
      session[:user_id] = nil
      if request.post?
        user = User.authenticate(params[:name], params[:password])
        if user
          session[:user_id] = user.id
          if session[:request_uri]
            redirect_to(session[:request_uri])
            session[:request_uri] = nil
          else
            redirect_to(:action => "index" )
          end
        else
          flash[:notice] = "Invalid user/password combination"
        end
      end
    end


Pretty straightforward, right?