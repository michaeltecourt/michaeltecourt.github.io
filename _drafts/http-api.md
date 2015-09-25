---
layout: post
title:  "HTTP APIs and REST"
date:   2015-09-30 00:00:00
categories: 
---

Yet another blog post about what REST is.	

## REST is synonym for "browseable"

Everyone already has a REST API in their website.  
Most chances are, your website is truly RESTful while your application programming interface is not.

All the constraints, details and nitpicking that will follow don't matter. REST is just an abstraction over "how the web works", 
how your browser interacts with a web server.

If your client needs to _understand_ anything more than the resource format, it ain't REST.  
Why ? because the motherf**ker will go down any time you alter some resource characteristics.  
Does you browser break down when I change HTML tags on my blog's page ? 
No, because your browser just understands well formatted HTML.
Same goes for atom readers, if an article changes the reader won't break down unless the resource is made of some invalid XML.
See the trend ? If the server sends back some resource with the correct format, then your client should not break.

_And how can I do that for my API ?_ Dude you probably can't, unless you extract some kind of abstraction over your data model 
and make it a full fledged media type. And why the hell would you want to do that ? 
you're probably doing RPC calls, and no one will blame you for that.

To understand why REST was such a major step forward in the way we built applications, 
you have to understand the context it was created in, and the needs it answered.

The _hypermedia as the engine of application state_ thing looks like a detail from now, 
but you have to remember that before the web network-based applications were built like your voicemail : 
The server had to track _state_ of where the client was in the navigation tree, to propose him options (or views).
Imagine the scalability issues if each of your apps had to store _which page each user is currently browsing_ !

Hyperlinks and URLs solved that. And the funny thing is, native mobile applications are technically a regression from REST.

You can set up a RESTful architecture without HTTP and pretty URIs.  
In fact, REST does not require HTTP. We can see the modern web as the reference implementation of REST, HTTP being the application protocol behind it.  

You could build RESTful applications over mainframe servers and IBM proprietary protocols, if you're into that.

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

## Kosher double cheese with bacon
REST has a number of key constraints that Dr. Roy T. Fielding summed up a academic (douchy?) manner.

A parallel with kosher would be : 

 * If it's pork, it ain't kosher
 * If you mix meat and milk, it ain't kosher
 
Therefore, trying to build a RESTful RPC service is like trying to make a kosher double cheese with pork bacon : you can't.

You can make it look like a bacon hamburger with turkey bacon.  
You can make it look like a crappy cheeseburger with soy cheese.  
In the end you can't make anything kosher with beef, cheese and pork at the same time.

Same goes for REST:

 * If you break your client by making changes that are still valid within your media type, it ain't REST
 * If you break your client by changing some resource URLs, it ain't REST

HAL is trying to fix a problem very few people actually need to solve : 
why the hell would you put extra effort to make your API RESTish with hyperlinks no-one will follow, 
when all you need is a bunch of HTTP endpoints for your clients ?  
Let's assume our non-restfulness and keep on building cool HTTP APIs. 
The world needed some interoperable standard for inter-application communication, 
and where the enterprise world failed with over engineered protocols, 
the web guys found a solution that was standing right before our eyes with plain HTTP.

