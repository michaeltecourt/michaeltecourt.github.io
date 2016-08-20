---
layout: post
title:  "Service Discovery and the forgotten URNs"
date:   2016-07-16 00:00:00
categories: 
comments: true
---

I like using Spring Boot / Cloud a lot. I won't dig too much into it, it's [2016](http://zeroturnaround.com/rebellabs/java-tools-and-technologies-landscape-2016-trends) 
and unless you've been living under a rock, it has become everything we all expected from Java EE for the last dozen years.  

Anyway, I'm currently working on adding service discovery to my customer's bunch of micro-services, 
and the [Eureka](http://cloud.spring.io/spring-cloud-netflix/spring-cloud-netflix.html#_service_discovery_eureka_clients) / [Feign](http://cloud.spring.io/spring-cloud-netflix/spring-cloud-netflix.html#spring-cloud-feign) integration is great.  
For a while I was wondering why the Spring team did not implement "HTTP Repositories" in [spring-data](http://projects.spring.io/spring-data/), 
but I guess they prefered working with Feign instead of reiventing the wheel.

My only grief would be a meaningless implementation detail, in the way that the service name/url is implemented : 

 * a service name must be provided for each client interface. By default, Eureka transparently provides the corresponding URL(s) associated with that service name
 * you can override the service discovery by providing directly the service URL (useful for testing)
 
These are two separate fields on the `@FeignClient` annotation :

```java
    /** 
      * In this case, Feign will use directly the URL 
      * instead of resolving the service named "myservice" with Eureka 
      */
    @FeignClient(name = "myservice", url = "https://myservice.api.example.com")
    public class MyServiceClient { }
```

Either a name or an address ? sounds familiar ?

## URI = URN or URL

A name or an address, that's exactly what a [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier#Relationship_between_URIs.2C_URLs.2C_and_URNs) is :

 * [URL](https://en.wikipedia.org/wiki/Uniform_Resource_Locator) : Location (address)
 * [URN](https://en.wikipedia.org/wiki/Uniform_Resource_Name) : Name

And how to use a URN ? with some kind of resolver, where Eureka fits the bill as a [non-standard](http://stackoverflow.com/questions/5476121/using-dns-as-a-urn-resolver) implementation :

 > A URN resolver is responsible for initiating and sequencing the queries that 
 > ultimately lead to a full resolution (translation) of a URN name or a "resolution request", 
 > e.g., a request for translation of a URN name into a URL.

Nothing here forbids some client side load-balancing : 1 URN -> different URLs

This could have been the day when everybody stopped googling "_what's the difference between URI and URL_", 
because nobody remembers what an abstract concept is before seeing a live example.

```java
    /** Use the service name with a URN. Yup, it's a valid URI. */
    @FeignClient(uri = "urn:my-enterprise:myservice")
    public class MyServiceDiscoveryClient { }
```

```java
    /** Use the service location with a URL. Still a valid URI. */
    @FeignClient(uri = "https://myservice.api.example.com")
    public class MyDumbClient { }
```

Poor URNs that never really worked out. It would have been cool to give new life to that unpopular internet standard.  
The only time I saw URNs in action was when putting together [SAML](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) single sign-on : few success outside the overly complicated, XML-driven enterprise world.  
Some governments agencies use URNs as unique identifiers, not very popular outside.

URNs and service discovery/client side load balancing made a lot of sense to me.  
A lot more sense than [inserting links that clients can't browse in API responses](https://spring.io/guides/gs/rest-hateoas/) (which most believe to be HATEOAS) : 
a waste of time when doing RPC over HTTP (which most believe to be REST)... but I'll leave that for [another blog post](/2016/08/08/http-apis-and-hypermedia.html).


