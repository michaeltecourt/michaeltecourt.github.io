---
layout: post
title:  "Introducing Cereebro"
date:   2017-05-14 00:00:00
categories: 
comments: true
---

# ![Cereebro](https://raw.githubusercontent.com/cereebro/cereebro.github.io/master/images/cereebro.png)

I'm proud to introduce version 1.0.0 of project [Cereebro](http://cereebro.io) !

## What is it ?

Keeping software documentation is an ageless problem. At the age of micro-services, it's easy to get lost within a distributed system, even if you designed it.

Cereebro automatically builds a [map](http://sample.cereebro.io) of a distributed system.

![cereebro-sample-screenshot](/images/cereebro-sample-screenshot.png)

## Why ?

Cereebro was born because of a strong motivation : laziness.

Working as a technical lead for the Lab (experimental) team at [edelia](http://edelia.fr) (EDF group) is pretty fun. 
We have more room than others team to make technical decisions, although at some point we may be asked to "do our chores" and follow part of the process like the others do.  
This is how I ended up being asked by the architecture team to maintain a list of all the micro-services built by our team, and the dependencies among them. 
Instead of taking a couple hours to fill an excel sheet, I asked for a small delay in order to build a tool that would do it permanently without risking any mistake.  
CTO Dominique Vienne granted us that delay, and my colleague [Luc Warrot](https://github.com/lucwarrot) was easily convinced by the idea of solving a boring task with open source code.

The name Cereebro came when I started working on the project and saw X-Men on TV. 
Charles Xavier was finding mutants with Cerebro the same way I wanted to find micro-services in our system.  
It was also consistent with the movie references that the Netflix team uses for their open-source tools (Zuul, etc...).  
Note that _cerebro_ means _brain_ in spanish, and actually it shouldn't be pronounced _cereebro_ like americans do... cerebro.io wasn't availble though !

After a couple weeks coding on the way home (and late at night) we had a working prototype and decided to stuff more features before release 1.0.0.
Although I severely underestimated how long the polishing part would take, I'm glad we made it production ready.

It was our first open source project, and it's already a great satisfaction to receive positive feedback from the community.  

<blockquote class="twitter-tweet" data-lang="fr"><p lang="en" dir="ltr"><a href="https://twitter.com/michaeltecourt">@michaeltecourt</a> <a href="https://twitter.com/springboot">@springboot</a> <a href="https://twitter.com/springcloud">@springcloud</a> <a href="https://twitter.com/NetflixOSS">@NetflixOSS</a> Thanks for your work, Cereebro is what i&#39;m looking for long time to have a dynamic view for micro services dependencies.</p>&mdash; Philippe Da Costa (@dacofr) <a href="https://twitter.com/dacofr/status/862560489288613889">11 mai 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

We hope Cereebro will be as useful to you as it is for us.

## How ?

We found all the pieces within the Spring eco-system :

 * the Spring Context contains mostly everything you need to detect an application's dependencies, it was just a matter of analyzing beans  
 * Spring Boot and `@Conditional` annotations made our relationship detectors elegant and lightweight
 * Spring Cloud and Netflix OSS gave us a central registry of all applications running in the system, and also a centralized place to publish the relationships we detected between those apps

Take a couple minutes to see how to [get started](http://docs.cereebro.io/Getting-Started), or find an even quicker summary below.

![cereebro-eureka-component-diagram](https://cereebro.github.io/images/cereebro-eureka.png)

### Cereebro Snitch

Add the Cereebro Snitch Spring Boot Starter to each of your applications in order to detect their relationships with other components :

```xml
<dependency>
    <groupId>io.cereebro</groupId>
    <artifactId>cereebro-snitch-spring-boot-starter</artifactId>
</dependency>

```

### Cereebro Server 

Create a Cereebro Server that will build a complete graph out of all the info snitched by applications :

```xml
<dependency>
    <groupId>io.cereebro</groupId>
    <artifactId>cereebro-server-spring-boot-starter</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```

Add `@EnableCereebroServer` somewhere on your configuration classes : 

```java
@SpringBootApplication
@EnableCereebroServer
public class CereebroServerEurekaClientApplication {
    // ...
}
```

## Learn more

Visit [Cereebro.io](http://cereebro.io) to find the [source code](http://cereebro.io) (Apache 2.0 License), 
how to [get started](http://docs.cereebro.io/Getting-Started), 
check out [samples](https://github.com/cereebro/cereebro-samples) and watch a [live demo](http://sample.cereebro.io), 
or read the detailed [documentation](http://docs.cereebro.io).

Cereebro releases are available on [maven central](http://repo1.maven.org/maven2/io/cereebro/).
