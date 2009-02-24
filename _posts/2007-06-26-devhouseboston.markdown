--- 
permalink: /posts/devhouseboston.html
title: DevHouseBoston
tags: 
- boston
- devhouseboston
- google maps
- rails
- where2go
layout: post
---
This past Sunday, I had the pleasure of attending [DevHouseBoston](http://devboston.pbwiki.com/).

So what's that you might ask. The way I like describing it is that it's pretty close to a LAN party, except with hacking on cool projects instead of online gaming.

Held near Central Square in Cambridge, MA, people started filtering in around 10am. At 10:20 or so, there were 20-30 people gathered, and a brief introduction was given, and everyone went around the room to introduce themselves and mention a project they would want to work on.

My project was to build a webapp to help you catalog the sights, sounds, etc, etc. My main use case was that I moved recently, and wanted a good way of getting an idea of what type of stuff is right around me as far as resturants, bars, etc go. Certainly, there is [yelp](http://www.yelp.com), and [Google Maps' search](http://maps.google.com), but neither really gave you a good view of exactly how much stuff was in an area (they usually limit the view to 10 or so per page).

As it turned out, two other attendees were interested in hacking on this.

Given that I've been into rails a lot lately, and rails is pretty good for getting something going pretty quickly, it was the obvious choice. And with the awesomeness of Google Maps API, that was a pretty obvious choice.

After a bit of research, I found two plugins which helped us out quite a bit. The first was [ym4r_gm](http://thepochisuperstarmegashow.com/projects/#ym4r).   We used this to be able to programatically construct a map in Ruby, and used the helpers in the view to render it.

For example, we had a method that looked like

    def build_map
      @map = GMap.new("map_div")
      @map.control_init(:large_map => true,:map_type => true)

      @map.center_zoom_init [@origin[0], @origin[1]], 12

      @markers.each do |marker|
        @map.overlay_init marker
      end
    end

In the head, we do:

    <%= GMap.header %>
    <%= @map.to_html if @map %>

This takes care of adding the appropriate CSS magic, and script includes, and spits out the javascript that actually does the constructing of the map. We are then free to add the map `div`, and things just work.

The second plugin was [geokit](http://geokit.rubyforge.org/). This provides `acts_as_mappable`, which adds a lot of magic to your model to let you be able to search for objects within a certain distance of an origin. For example:

      @spots = Spot.find :all, :origin => @origin, :conditions => "distance < #{params[:distance]}";

It also provides a facade for using the various geocoders that are available, such as Google, Yahoo, and Geocoder.us.

So, by making use of these, we were able to getting a somewhat functioning prototype working by the end of the day.

We had collected data on a few chains restaurants/eateries (since the data is so easy to grok) that spread across Boston. In the webapp, you select Boston, and it shows all the Spots that we had in the system. You can set a center of the map, and choose how large a radius to search for, and a name search. As I type this, it sounds pretty trivial, but let me assure you, it did take quite a bit of effort to get going :)

One thing we needed was some way of sharing our work through the day. I had seen some projects being hosted by [Google Code](http://code.google.com), so after a few minutes of poking around, I had setup a new project for us, [where2go](http://code.google.com/p/where2go/). It sounds like my fellow collaborators are interested to continue hacking at this, so with luck, we will eventually launch something cool and exciting.

All in all, it was a great time, and many thanks to all the organizers and attendees. I am definitely looking forward to the next DevHouseBoston, whenever it may be.