---
layout: post
title:  "Y U NO USE UML"
date:   2016-08-06 00:00:00
categories: 
---

While a picture is worth a thousands words, a good diagram is better than : 

 * a couple hour-long meetings
 * six e-mails 
 * a 2MB PDF no developer will ever open
 * the three above together

After pulling my hair speaking with product managers about interpretations we couldn't agree on,  
after endless arguments with architects about what the clean design should have/could have been,  
after talking for too long about the pros and cons of solutions that would never work,  
I've found out that it's much more productive to starting scribbling on the white board instead of debating through the air.

Most programmers agree, but the problem is that we have that tendency to re-invent the wheel when it comes to sketching what we have in mind.  
Having worked recently on authentication and authorization projects on a fairly large system, I encountered a LOT of unique pieces of office art over the past few months. 
The enterprise world is full of unexpected creativity.

## We've all been there

_Hey there's that large process involving a handful of components with complex interactions, 
let me draw five boxes and link them up as ideas come out of my mind !  
And now that the thing has become a spaghetti ball nobody can understand, I'll add numbers on top of the arrows, 
and each number will have a legend with large paragraphs I'll put in a word file !_

Optional :

 * Vivid colors for extra-level of confusion
 * Servers depicted as 1985 computers, globe icon representing the outside world, various shapes of clouds
 * All arrows pointing both ways, killing any chance of finding a starting point amid the chaos


No kidding, the diagram below was like the 3rd result when I googled "Software Architecture Diagram".  
Despite its simplicity, it is already an effort to figure out what the arrows mean :

![Live Example](http://image.slidesharecdn.com/diagramming-140128130130-phpapp01/95/a-software-architects-view-on-diagramming-9-638.jpg?cb=1396270322)

To be fair, the target audience of this diagram is probably not programmers, 
maybe managers who want to get an idea of what happens under the hood.  
The problem is, people use this kind of diagram as technical documentation / blueprints.

![y-u-no-use-uml](/images/y-u-no-use-uml.jpg)

Unfortunately you can't put everything on the same diagram and expect it to be clear.  
We rarely pull out a diagram for something too trivial, 
and good diagrams follow the same rules as good object design : separation of concerns.  
Sorry, you will probably have to draw 2 or 3 different diagrams to represent each aspect of a complex feature.

From [Wikipedia](https://en.wikipedia.org/wiki/Unified_Modeling_Language) :

 > Structure diagrams emphasize the things that must be present in the system being modeled.  
 > Since structure diagrams represent the structure, they are used extensively in documenting 
 > the software architecture of software systems.
 
 ...
 
 > Behavior diagrams emphasize what must happen in the system being modeled. 
 > Since behavior diagrams illustrate the behavior of a system, 
 > they are used extensively to describe the functionality of software systems.

For the sake of our minds, **Don't mix structure and behavior together on one diagram.**

In other words :

 * don't mix up the technical and business stuff in one do-it-all scribble
 * don't try to describe _what_ you do and _how_ on the same picture

I'm no UML purist to be honest (do they still exist ?), I don't think you should *always* hit your modeling tool, 
or sharpen your pencil, before coding.  
Heck, I just draw diagrams when words fail, and it's far from perfect.  

Here are the ones I often use, depending on what I want to express.

_Note : This is based on my very practical and personal enterprise experience._  
_UML experts / college teachers would probably lapidate me on sight for my many imprecisions._  

## Useful UML diagrams

### Sequence Diagram (behavior)

**Use case** :  _I want to visualize the chain of interactions between actors during a process_

![Sequence Diagram](/images/sequence-diagram.png)  

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

 * _For this call, where do we find this parameter ? it has not been provided_
 * _How can the client track the order if we didn't send him the generated ID ?_
 * _This flow is plain impossible, it would force us to send a password cleartext_
 * _We need to make three server calls here, we can save round trips by storing some data the first time_

I don't like uncompromising rules, but I wouldn't start implementing something complex involving 
third-parties before agreeing on a precise Sequence Diagram first.

### Component Diagram (structure)

**Use case** : _I have a bunch of components and I want to picture them with their dependencies._

This diagram depicts what are the components of a given system and how they are linked through their interfaces.  

#### Application Scope (probably not the right way to call that)

![Component Diagram](/images/internal-component-diagram.png)  
_Who care about lollipops and interfaces ?_

While looking at an application's internals, component diagrams are cool for design discussions early on, 
as you can shape up the main modules and dispatch each component to different developers.  

Component diagrams can be much more precise than just dependencies, by picturing which interfaces are shared with whom.  
I've tried a couple times, and it gets too fuzzy in my opinion.

#### System scope (probably not the right name either)

![Deployment Component Diagram](/images/system-component-diagram.png)
_Notice how the cool looking component icon is pointless here._

At the system level, I like to use component diagrams to display application/resources dependencies.  
To be honest, it's kind of a mix between a simplistic Component and **Deployment Diagram** (machines are just physical components anyway).
In a microservice architecture, where every functionality is potentially broken down into multiple HTTP applications, 
having a representation of the big picture is really helpful.  

**Whatever the scope is, just don't try to describe a business process on top of a component/deployment diagram !**

### Activity Diagram (behavior)

**Use case** : _I want to zoom in on this complex process and figure out all the possible scenarios_

![Activity Diagram](/images/activity-diagram.png)

I don't often get to draw activity diagrams, but I when I do, 
it's probably because I'm trying to make some sense out of a poorly described business process.  

Good news, regular humans can read it without help. Better, business guys can draft it themselves. 

### Class Diagram (structure)

**Use case** : _I still think object oriented programming should rule the world and 
I want to impress people with the dozen design patterns I shoe-horned in my new library._

![Class Diagram](/images/class-diagram.png)

Or more seriously, if you're working with peers on the same codebase and you want to agree on fine grain design, 
class diagrams offer a nice way to get started on a white board.  
I usually throw these diagrams away at the end of a short design meeting or when done implementing, 
as IDEs do a better job than us at reverse engineering them from the code.

## The rest

That's only my opinion, but I find the other diagrams overkill.  
Thanks to [this site](http://creately.com/blog/diagrams/uml-diagram-types-examples), here is a list I couldn't have remembered on my own : 

 * Use Case diagrams : I find Use Case Diagrams more confusing than text. 
   Actors, features and roles can easily be broken down in tables and bullet points. 
   Also, the principal stage for this diagram is early product design, 
   and business guys don't buy into abstract techie methodology pushed down their throats (me neither). 
 * State diagrams : Similar to activity diagrams that I prefer.
 * Package diagrams : I find the code self explanatory in most cases, which awfully sounds like lazyness.
 * Communication diagrams : the exact same can be produced with a sequence diagram, in a much clearer fashion.
 * Timing diagrams : aren't diagrams supposed to make things easier to understand ?!
 * Profile diagrams, composite structure diagrams, interaction overview diagrams, object diagrams : seriously, who has the time for this stuff in a real world project ?

## Tooling

Finding the right tool for the job has been a pain : UML modelers are either expensive, not portable, plain crap, or all three.  

[https://draw.io](https://draw.io), a free web-based diagram editor, is pretty much everything 
I've been expected and then some (it helped me design my kitchen too).  
The UI is smooth, you find everything you want right away. Lightweight and effective.  
You can export the diagram image with XML source embedded -- check out with the diagrams above.  
Unfortunately, drawings are cool the first time around but they are a burden to maintain, 
and the XML representation cannot be reasonably manipulated by hand.

Although [PlantUML](http://plantuml.com) is old, don't let the website scare you away, 
it's still the best solution around, especially its integration with [AsciiDoctor through a maven plugin](https://github.com/asciidoctor/asciidoctor-maven-examples/tree/master/asciidoctor-diagram-example)
(thanks to [Andres Almiray](http://jroller.com/aalmiray/) for the great talk at Voxxed Days Berlin 2016).
Writing diagrams using text is clearly the most maintainable approach.  
The bad : diagrams look like their website, and compiling them to images require the native Graphviz/dot library installed on your machine.
It will be great once they finally rewrite dot in some portable language, which looks like a [daunting task](http://fr.plantuml.com/smetana01.html).  

Or if you're in the mood, you can generate diagrams from ascii art using [Ditaa](http://ditaa.sourceforge.net/).  
Unproductive but funny. Integration with AsciiDoctor is available too.

## Conclusion

If you feel like drawing is more efficient than words when it comes to documenting software, 
let's try to do it in a way most of us can understand right away.  
The UML guys had the diagram thing covered for two decades. If UML is overkill, simplify the syntax of a diagram type without losing sight of the key information you want to share.

And if you should only remember one thing out of this : **Don't mix structure and behavior together on one diagram.**
