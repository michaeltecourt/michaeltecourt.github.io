---
layout: post
title:  "HTTP APIs and Hypermedia"
date:   2016-08-08 00:00:00
categories: 
---

I have been trying to finish this blog post forever, and the motivation was sparked by a [tweet from Spring's Oliver Gierke](https://twitter.com/olivergierke/status/761860716169162752), thanks a lot !

<blockquote class="twitter-tweet" data-lang="fr"><p lang="en" dir="ltr"><a href="https://twitter.com/olivergierke">@olivergierke</a> in brief : We like service registries because we do RPC. We care less about hypermedia because actually we don&#39;t do REST. Easy</p>&mdash; Michael Tecourt (@michaeltecourt) <a href="https://twitter.com/michaeltecourt/status/761880373735424002">6 août 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

_Note : While checking the definition of [RPC](https://en.wikipedia.org/wiki/Remote_procedure_call) 
I realized that the concept was not just about calling remote services, 
it came along with the [crazy](http://martinfowler.com/bliki/FirstLaw.html) idea 
of making programmers forget about network constraints. 
I'll keep using the acronym RPC for "remote service calls" as most people get it._ 

Solutions like Eureka are popular because they solve a concrete configuration problem in the world of remote/micro services : 
instead of configuring the address of each service consumed by your application, each service checks-in with Eureka, 
and your application queries Eureka by to know where services are located.  
Configuration goes from N URLs to 1, at the price of a couple `spring-cloud-starter-eureka*` dependencies : easiness wins, like always in programming.  
Also usual in programming, fancy names can be misleading : _service discovery_ is more about _service address discovery_ or _service registration_ depending on the angle.  
Applications do not discover brand new services and learn how to use them on the fly, 
applications are coded to call certain services and expect certain responses out them. 
Only the "root" address of those services is configured "lazily" at runtime. Concrete problem, concrete solution.

On the other hand, what Oliver Gierke calls _resource discovery_ here is guided by a more theorical quest : how to get RESTful.  

Let's put some context first and clarify what we've been doing with remote services and HTTP for the past decade.

## Where we at : JSON over HTTP

Remember the mid 2000s when SOAP was all the rage and we realized we could make HTTP calls 
between javascript client and servers with [XHR](https://developer.mozilla.org/fr/docs/Web/API/XMLHttpRequest) ?

Easiness kicked out  XML and SOAP (a.k.a. XML-RPC) from the picture, as the up and coming JavaScript guys established their 
own way for calling remote services, based on their path of least resistence : JSON over HTTP.  

The whole point was to consume the same services from the browser as from other servers, 
potentially written in different languages. 
We were finally having the interoperability standard that 
enterprise guys had wanted for years through the likes of RMI/Corba/SOAP. 
That standard just came in unexpected fashion from web folks.  
Back then RPC (Remote Procedure Call) was not a bad word, in fact it was what we were doing (proof, [JSON RPC](https://en.wikipedia.org/wiki/JSON-RPC)), 
and it still applies to what we do with public HTTP APIs. 
Our need is to remotely call services coded by other people, and we use HTTP for transport because it proved to be the easiest.

People became judgemental with RPC when the word REST came out.

## What REST means : "browsable"

Ever since somebody dug out Dr. Roy T. Fielding's [thesis](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) in the same period, 
the words REST/RESTful have been the subject of a lot of debate on the web.

[This talk by Stefan Tilkov](https://www.youtube.com/watch?v=pspy1H6A3FM) shoots down a bunch of common misconceptions, 
even though I don't necessarily agree on everything that follows.  
The most important misconception is that `REST = HTTP + pretty URLs`.

To define REST in one word : **browsable**.  
"Representational State Transfer" is the set of constraints that a client/server architecture must observe to follow a stateless browsing model.  
The reference implementation of a RESTful application is a **web site**, the reference implementation of a RESTful client is a **web browser**.  
Your API is RESTful if it can be browsed. Plain and simple. Everything else is a corollary.  
As we all know [naming things](http://martinfowler.com/bliki/TwoHardThings.html) is one of the hardest things in programming, 
that's why finding such a catchy name might already be worthy of a PhD by itself.  


As [per Fielding himself](https://www.infoq.com/articles/roy-fielding-on-versioning) : 

 > A RESTful API (done right) is just a website for clients with a limited vocabulary.

For some reason, ever since JSON/HTTP services became popular, web developers have wanted it to be REST.  
Like if REST was an instant proof of good design or something. Roy Fielding never 
stated that RPC was _bad_, he said it was not REST (don't piss off the guy by using the term incorrectly, 
or he will shower you in with academic jargon).

## HATEOAS

In that quest for the almighty RESTful-ness, some web folks read that we [needed hypermedia to make our APIs RESTful](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven). 

Let's first translate "Hypermedia As The Engine Of Application State" into something less cool.  
All it means is that an application's state, i.e. "where you are in the application", must be defined by links.  
Sounds familiar ? HATEOAS is a complicated way of describing how your browser works. 
The browser itself only has to follow links to change the state (current page) of the application (site) you are browsing. 
The browser itself does not know in advance how to navigate a site, it just follows the links you click, 
as each request generated by following links are self sufficient, 
the server can transition to a new page without knowing where you were before.
**HATEAOAS means that following links tell the application what to do next**.

REST in the form of HTML is meant to drive a _whole application_, especially view transitions.  
The principle of browsing through links and URIs is a huge upgrade from stuff like voicemails or 
mainframe applications, where servers have to keep track of where each user is in a navigation tree.  
It also solves the issues of updating installed applications, because you reload the applications (or at least portions) with each request.

So far, so good.

## Where I get lost : Applying HATEOAS to services

What I don't understand is when I read that hypermedia _should_ be applied to HTTP APIs to "lower coupling" 
and make APIs more "evolvable".

Hypermedia is a great representation of **choice**.  
We can argue a list of links  is more evolvable and extensible than this command line interface : 

 > michaeltecourt$ mvn archetype:generate  
 > [INFO] ...  
 > [INFO] Generating project in Interactive mode  
 > [INFO] ...  
 > Choose archetype:  
 > 1: remote -> am.ik.archetype:maven-reactjs-blank-archetype (Blank Project for React.js)  
 > 2: remote -> am.ik.archetype:msgpack-rpc-jersey-blank-archetype (Blank Project for Spring Boot + Jersey)  
 > 3: remote -> am.ik.archetype:mvc-1.0-blank-archetype (MVC 1.0 Blank Project)  
 > 4: remote -> am.ik.archetype:spring-boot-blank-archetype (Blank Project for Spring Boot)  
 > ...  

The main reason most of us don't use hypermedia for APIs is the same reason 
we don't use command line interfaces in _interactive mode_ when scripting : 
choice is for intelligent clients.

### Browser vs "Programmatic client"

I'll call _programmatic client_ an application coded to consume a remote service in a certain way.  
The idea is to compare it with how a _browser_ operates under your guidance.  

Unlike a human who truly discovers links with its eyes, thus removing the need for the browser to "understand" a page's data, 
a JSON response has to be interpreted by a machine who can only do what it's coded for.  
Programmatic clients are intrinsically coupled to a server by _what they call it for_. 
You can fool yourself by pretending "clients can browse their way to a resource by following links from the entrypoint", 
the truth is that these programmatic clients will never wander further than what their source code says.  
These clients will never _understand_ new resources types, unless of course you bake some AI into it.  

 * A web browser calls a server for a resource without expectations, and renders it for your eyes.  
   The web browser is decoupled from servers and data, it is only coupled to the media type (response format like HTML).  
   The web browser processes each resource within a given media type the same generic way.  
   _You_ allow the browser to be decoupled because you can figure out the meaning of links on the fly.
 * A programmatic client calls a server for something _specific_ inside a resource, 
   to do something specific with it, based on what it's been coded for.  
   Business logic on clients is not **driven** by hypermedia, but by the client's source code !  
   Hypermedia is not the engine here, it's data.    
   In the best case, links allow programmatic clients to “find out” relative URLs of services they had previous knowledge of : 
   less client-side configuration at the cost of more complexity.  
   In the worse case links are not used, as developers who write clients will hard code a direct call to a certain services, based on what they need.

We lose our minds when we translate REST to JSON, let's translate what we do with HTTP APIs to the good old web.  
Applied to HTML, programmatic HTTP clients are similar to slurping a site's content, 
by fetching an HTML page using HTTP GET on its URI, then picking up a `div` element by name, and trying to parse its content. 
That approach is a far cry from _how the web works_, no matter how you turn it.  
Adding links won't change the fact that clients expect precise elements in response messages, because they are coded for a finite number of things.

One key constraint of REST is that your client should only understand the media type returned by the server, not the "content". 
For example, your browser does not understand what [gleamfootball.com](https://www.gleamfootball.com) is about, 
nor does Chrome break if a `div` element disappears from one request to the next. 
If it's valid HTML, the browser won't break, no matter the content.  
This is not true for our APIs. Our clients do have an understanding of which resource does what, 
as they expect a field name `username` out of the `/user` resource, not just valid JSON.  
I don't see how to remove that "coupling", because that coupling on data is exactly _why_ you are consuming the resource in the first place.

**Do we even need clients to be _more_ decoupled from servers ?**  
Let's not lose sight of [where we at](#where-we-at--rpc-over-http), 
~99% of us are just sharing remote service endpoints with other folks. Google does. 
I use Google Maps' API, and I don't want to browse for the sake of browsing.  
I want my code to call _this_ precise service to obtain _that_ piece of data I want : GPS coordinate out of an address. 
I would argue that the service's relative path `/maps/api/geocode/...` is a part of its signature, just like a method name in Java.  

### Solving the API versioning issue

I disagreed with Oliver Gierke on how hypermedia would solve the API versioning problem, 
and tweets were too short to express myself.  

The API versioning drama came after [this interview of Roy Fielding](https://www.infoq.com/articles/roy-fielding-on-versioning). It made a lot of people cry.  
At some point we were all religously adding something like `/api/v1` into our pretty URLs, and we felt it looked awseome, then Fielding said it was wrong. The internet's daddy was slaying our sand castle.  
Like everytime Fielding disagrees on something, few understand why, but everybody nods their head. 
In Fielding's view we should plan for change and design for decades, and that hypermedia solves the versioning problem. _(*)_  
Yes, hypermedia solves the problem when REST is used at the application level, not when HTTP is only used for data transport like we do.  
When you visit the [stackoverflow.com](http://www.stackoverflow.com), the whole application (pages) are delivered through HTTP, not only the data. 
If the guys at stackoverflow shipped a bug in the previous version of the site, all you have to do on the client side is reload the page. 
The updated version of the site will still be HTML and the browser won't break. 
That's why you don't name it _stackoverflow.com/questions/v1_, 
it does not make sense for HTML resources, clients send a request again and they are up to date.

It does not apply to what we do with HTTP APIs, which are just a set of remote endpoints, consumed by clients with expectations on response content.  
If you got it wrong the first time and need to make non backward-compatible changes to your remote service's signature, it will break clients, 
no matter if the change occurs in URLs, message format, media type or protocol.   
A friend of mine often says that most programming issues are located "between the chair and the keyboard". 
The problem of versioning HTTP APIs, or any public API for that matter, falls in this category in my opinion.  
No architecture or framework solves inadequate design.  

Adding links alone won't solve the **the root cause of most API versioning : ever-evolving data models consumed by third parties**.  
Even if you designed a complete modeling language out of your business model like `application/mybusiness+json`, 
and used it as media type, your business object model probably evolves on a weekly/monthly basis, 
unlike the HTML spec that serves a very generic purpose.  
Congratulations, the versioning issue has been moved from URLs to media type !  
REST won't solve the issue, careful design _can_ along with a profound understanding of the business.  
Some highly generic use cases may be worth the price of being browsable : if a stable media type can be extracted out of the domain, 
like atom for news, and if the API needs to be browsable by business requirement (not just developer fantasy), then you
should probably go the extra hundred miles and build a specification for a RESTful client (browser), 
by creating a [full fledged new "protocol"](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven) :

 > A REST API should spend almost all of its descriptive effort in defining the media type(s) 
 > used for representing resources and driving application state, 
 > or in defining extended relation names and/or hypertext-enabled mark-up for existing standard media types.
 > Any effort spent describing what methods to use on what URIs of interest 
 > should be entirely defined within the scope of the processing rules 
 > for a media type (and, in most cases, already defined by existing media types)

In other less-precise words, when building a browsable API, you should thoroughly describe your new media type because 
it is mostly the only thing your browser (client) knows how to deal with, along with lower level protocols.  
Good luck with that. I have yet to see (or recognize) a case where the business warrants such an effort.  
My conviction is that browsing requires some degree of intelligence, whether it's artificial or yours. 
So far the only relevant RESTful machine-to-machine interaction I can think of is what search engines do by crawling the web. 

When it comes to user-facing applications though, if you want zero coupling with the server, 
the 100% RESTful solution stands right before your eyes.

## How to make an API RESTful then ?

If you want to lower coupling between your end-user application and server, 
remove client-side intelligence altogether by making it a browser.  
To remove all coupling with any business-related model suceptible to change, 
the server should directly respond with view representation and actions embedded as hypermedia, 
instead of only data representation. 
And unless you have a plan to reinvent a "view modeling language", use HTML so your client is just... 
a web browser... which makes your server application a web site.  
We got back to where we came from, if you want pure REST, and the lowest client/server coupling ever, go back to the good old web.  
In my opnion, [a lot of mobile applications](/2015/09/25/mobile-apps-madness.html) are technically a regression from REST, 
in the sense that they could be only web applications, but that's another story.  
Even within a RESTful web site, [code-on-demand](http://whatisrest.com/rest_constraints/code_on_demand) (JavaScript) can make RPC calls to servers.  
RPC is not bad in itself, bad design is. Get over the fact that you are not RESTful everywhere.  
Embrace your RPCness and design APIs carefuly with your fellow programmer in mind.  
At this point we are all doing RPC the same way with JSON over HTTP, and I think it's already wonderful that we got there.

Is REST really the next level of APIs ? I don't know, why are we not using CLI interactive mode when scripting ?  
All I know is that it would require much more than hypermedia from servers, 
clients would need to be a hell of a lot smarter too. Not only about following links, also in the way they process resources : 
maybe learning concepts instead of relying on pre-defined fields.   

Oddly enough, my opinion has already been addressed by Fielding in typical fashion :

 > Roy: I talk about it because the initial reaction to using REST for machine-to-machine interaction is almost always of the form 
 > "we don’t see a reason to bother with hypermedia — 
 > it just slows down the interactions, as opposed to the client knowing directly what to send." 
 > The rationale behind decoupling for evolvability is simply not apparent to developers who 
 > think they are working towards a modest goal, like "works next week" or "we’ll fix it in the next release".
 >  
 > If developers can conceive of their systems being used for a much longer time, 
 > then we can escape their own preconceptions about how it will need to change over time. 
 > We can then work back from decades to years (how long until you don’t know your users?) 
 > or even months (how long until you’ve lost control over client deployment?).
 
... I'll just go with the rest of the internet on this one, and nod my head in silence like I get it.

And if you want to do RPC and pretend it's REST... I'll call it the 
_[kosher](https://en.wikipedia.org/wiki/Milk_and_meat_in_Jewish_law) 
bacon-cheeseburger dilemma_ : some constraints make some concepts incompatible with others.
  
- - -  
  
_(*) On a sidenote : the most concrete tip I could extract out of Fielding's [interview](https://www.infoq.com/articles/roy-fielding-on-versioning) is that if you do need to break backward-compatibility, 
ship the application as a new one, and put a version number in the hostname instead of adding weight to each relative path "just in case". Seems legit._