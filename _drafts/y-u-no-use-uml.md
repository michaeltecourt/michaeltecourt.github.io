---
layout: post
title:  "Y U NO USE UML"
date:   2015-09-22 21:40:00
categories: 
---

While a picture is worth a thousands words, a good diagram is often better than : 

 * a couple hour-long meetings
 * six e-mails 
 * a 2MB PDF no developer will ever open
 * the three above together

After pulling my hair speaking with product managers about interpretations we couldn't agree on,  
after endless arguments with architects about what the clean design should have/could have been,  
after talking for too long about the pros and cons of solutions that would never work,  
I've found out that it's much more productive to starting scribbling on the white board instead of debating through the air when a problem arises.

Most programmers agree, but the problem is that we have that tendency to re-invent the wheel when it comes to sketching what we have in mind.  
Having recently worked on authentication and authorization projects for a large company, I encountered a LOT of unique pieces of office art over the past few months.

## We've all been there

_Hey there's that large process involving a handful of components with complex interactions, 
let me draw five boxes and link them up as ideas come out of my mind !  
And now that the thing has become a spaghetti ball nobody can understand, I'll add numbers on top of the arrows, 
and each number will have a legend with large paragraphs I'll put in a word file !_

Optional :

 * Vivid colors for extra-level of confusion
 * Servers depicted as 1985 computers, globe icon representing the outside world (that's deep)
 * All arrows pointing both ways, killing any chance of finding a starting point amid the chaos

![y-u-no-use-uml](/images/y-u-no-use-uml.jpg)

Unfortunately you can't put everything on the same diagram.  
We rarely pull out a diagram for something too trivial, 
and good diagrams follow the same rules as good object design : separation of concerns.  
Sorry, you will probably have to draw 2 or 3 different diagrams to represent each aspect of a complex feature.

I'm no UML purist to be honest, I don't think you should *always* hit your modeling tool (or sharpen your pencil) before coding.  
Heck, I just draw diagrams when words fail, and it's often far from perfect.  

Here are the ones I often use, depending on what I want to express.

## Useful UML diagrams

### Sequence Diagram

**Use case** : 

 * _I want to visualize the chain of interactions between actors during a process_

This is the one you want to bring for that long meeting with other teams about how you'll implement the latest fancy Single Sign-On protocol.  
It can of course document any kind of interactions between applications, components, classes or whatever you want.
If I had to keep only one type of diagram it would be this one.  
It literally saved me days of :

 * explaining which actor does what
 * arguing on solutions that suck : a sequence diagram visually reveals the complexity of bad solutions
 * arguing on interpretation : no room for doubt when each call is written along with its parameters
 * most importantly, starting a development that would prove to be impossible

Let me emphasize this last point : when done with care, a Sequence Diagram can point out any missing element in a complex process before you start coding.  
The logic of the process will be described step by step, forcing your consciousness into every detail :

 * _For this call, where do we find this parameter ? it has not been provisioned._
 * _How can the client track the order if we didn't send him the generated ID ?_
 * _This flow is plain impossible, it would force us to send a password cleartext !_
 * _We need to make three server calls here, we can save round trips by storing some data the first time_

I don't like uncompromising rules, but I wouldn't start implementing something complex involving 
third-parties without drawing a precise Sequence Diagram first.

### Component Diagram

**Use case** : _I have a bunch of components and I want to picture them with their dependencies._

This diagram depicts what are the components of a given system and how they are linked through their interfaces.  
It helps you keep in mind _who_ does _what_.

Great for design discussions early on, as you can shape up the main building blocks of a project, 
share the big picture, and dispatch elements to different people.

At the age of microservices, where every functionality is potentially broken down into multiple HTTP applications, 
it can be a really helpful to have a representation of the whole.

### Deployment Diagram

**Use case** : _I want to know where my components are._

The deployment diagram is about _where_ components are physically installed, not _what_ they do.  

If you have a huge list of VMs containing a myriad of apps somewhere in a public cloud, 
a deployment diagram can be your sky map.  
It can also be useful if you have to talk with the ops team, 
or you need to track packages in a distributed environment.  

### Activity Diagram

**Use case** : _I want to zoom in on this complex process and figure out all the possible scenarios_

If you're into Business Process Management (BPM) tools, 
chances are you have no choice but to begin with BPMN, which 
[shares a lot of similarities](http://www.sparxsystems.com/enterprise_architect_user_guide/9.2/model_simulation/bpmn_simulation_comparison.html) 
with Activity Diagrams.  

Algorithms are also good candidates for Activity Diagrams. They can help you find out edge cases and unhandled errors.  
The focus is clearly different from a Sequence Diagram, which often aims to describe the steps of the "main case" in a linear fashion.  
Activity Diagrams stand at a higher level, the technical details are not thoroughly enumerated.

Good news, regular humans can read it without help.  

### Class Diagram

**Use case** : _I still think object oriented programming should rule the world and 
I want to impress people with the dozen GoF design patterns I shoe-horned in my new library._

Or more seriously, if you're working with peers on the same codebase and you want to agree on fine grain design, 
class diagrams offer a nice way to get started on a white board.  
I tend to throw these diagrams away when implementation is over, 
as modern IDEs do a better job than me at reverse engineering it from the code.

### Those I don't really like

#### Use Case Diagram
That's only my opinion, but I find Use Case Diagrams more confusing than text. 
Actors, features and roles can easily be broken down in a table with a handful of bullet points for each row.  
Also, the principal stage for this diagram is early product design, and business guys rarely buy into it.  
They should have the final say in which solution fits their need, just like developers for technical solutions.

#### The rest

Once again this is subjective but I find the other diagrams overkill.  
Thanks to [this site](http://creately.com/blog/diagrams/uml-diagram-types-examples), here is a list I couldn't have remembered on my own : 

 * State diagrams : Similar to activity diagrams that I prefer.
 * Package diagrams : I find the code self explanatory in most cases, which awfully sounds like lazyness.
 * Communication diagrams : the exact same can be produced with a sequence diagram, in a much clearer fashion.
 * Timing diagrams : aren't diagrams supposed to make things easier to understand ?!
 * Profile diagrams, composite structure diagrams, interaction overview diagrams, object diagrams : seriously, who has the time for this stuff in a real world project ?

## Tooling

Until recently I struggled to find a good tool for the job.  
UML modelers were either expensive, not portable, or plain crap.  

Then came [https://draw.io](https://draw.io), a free web-based diagram editor.  
It's pretty much everything I've been expected and then some.  
The UI is smooth, you find everything you want right away. Lightweight and effective.  
You can save the diagrams to a number of platforms : Google Drive, Dropbox, browser, file system...
The diagrams can also be exported in any format you want (png, pdf, html...) 
and the XML source can either be downloaded separately (nice for versioning in a git repository) 
or embedded directly in image files.

## Conclusion

If you feel like drawing is more efficient than words when it comes to documenting software, 
let's try to do it in a way most people can understand right away.  
Personally I found out the hard way that the UML guys had the diagram thing covered for two decades.
