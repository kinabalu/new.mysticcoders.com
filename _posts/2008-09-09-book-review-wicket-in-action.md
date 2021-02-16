---
layout: post
status: publish
published: true
title: 'Book Review: Wicket in Action'
author:
  name: Craig Tataryn
  picture: '/assets/blog/authors/ctataryn.jpg'
  url: https://mysticcoders.com
date: '2008-09-09T10:35:35.0000Z'
tags: []
comments: true
---
<a href="http://www.mysticcoders.com/wp-content/uploads/2008/09/wicket-in-action-title-page.png"><img class="alignleft size-medium wp-image-85" src="http://www.mysticcoders.com/wp-content/uploads/2008/09/wicket-in-action-title-page.png" alt="" width="249" height="300" /></a>

As the Manning publications book Wicket in Action is soon to hit the shelf of your local book store, I was granted the opportunity of a sneak preview.&Acirc;&nbsp; Now, I'm not a person with a lot of spare time on my hands these days but I felt an obligation to give this book a serious read because A) Wicket is an excellent framework for building web applications B) The people who wrote this book are extremely helpful on the mailing list and irc channel and I wanted to know if their talents translated well to book writing.&Acirc;&nbsp; I'm happy to report it has, in spades.

Wicket in Action is a very well organized and it's examples and diagrams are well thought out.&Acirc;&nbsp; I can't tell you the number of books I've read where they use diagrams to simply fill up pages instead of using them to hammer home a point they are trying to make.

The impression I got throughout the book was that the authors wrote the book from the point of view of where they were a few years ago when Wicket was first being introduced to the world.&Acirc;&nbsp; That is, they don't take much for granted and really do make an excellent effort to explain Wicket to the reader as if they are looking at it from "new eyes"&Acirc;&nbsp; They have an almost prescient sense of what questions are running through your head as you are going through their examples.&Acirc;&nbsp; Same with "gotchas", for instance the '404' you'll get the first time you point your browser to localhost:8080 without specifying the context path of your app.&Acirc;&nbsp; So many books will skip over the many "gotchas" you encounter when first starting with a new technology, but not this one.

The layout of the chapters are spot on, they take you right from the basics and lead you through the steps it takes to get your first Wicket application up and running. This includes explaining how to setup a Wicket project in both Ant and Maven which is sure to please a majority of readers as a "<a href="http://manning.com/dashorst/Wicket_Bonus-chapter15.pdf">bonus chapter</a>".&Acirc;&nbsp; In this chapter they take great strides to explain how the various configuration files (really just web.xml) are configured and why they are configured thusly.

The progression of the book flows nicely, from explaining how Wicket works in behind the scenes (i.e. what a RequestCycle is), to showing you an application that uses Wicket and it's components.&Acirc;&nbsp; They also have specific chapters which dive in-depth into components of the framework which you might have questions about after seeing the example application make use of them.&Acirc;&nbsp; I really like this type of writing, I like seeing an example then an examination or dissection of the code.&Acirc;&nbsp; For example after seeing the "Chessr" application being built, they come back full circle to explain what a "Model" is and which ones you get out of the box in Wicket, how they work and any shortcomings they present.

Chapter 7 really shows the authors are in tune with their developer community.&Acirc;&nbsp; The most confusion area of Wicket centers around "Reusable Markup" (panels and fragments) and the authors definitely deliver a concise explanation.&Acirc;&nbsp; I remember a few years back reading <a href="http://www.nabble.com/Attempted-summary-of-multiple-%3Cwicket%3Achild--%3E-thread-to13637194.html#a13637194" target="_blank">this thread </a>and after I waded through the Wicket documentation (and source code!) I had a decent understanding of how the advanced markup composition worked.&Acirc;&nbsp; Luckily for the reader of this book,&Acirc;&nbsp; Chapter 7 clears the cobwebs and makes the topic easy to understand.

Another great thing the authors did was make code fragments extremely easy to interpret.&Acirc;&nbsp; They use a technique which I am very fond of, and that's annotated code fragements, an example of which can be seen below:

<div class="mceTemp">
<dl>
<dt><a href="http://www.mysticcoders.com/wp-content/uploads/2008/09/wicket-in-action-annotations.png"><img class="size-medium wp-image-84" src="http://www.mysticcoders.com/wp-content/uploads/2008/09/wicket-in-action-annotations.png" alt="Annotated Code Samples" width="300" height="100" /></a></dt> </dl></div>
At a glace you know where to look in the paragraph below the code fragment for an explanation of the part you are interested in.

The authors did a great thing in that there is not an API chapter or appendix.&Acirc;&nbsp; So many a time have I bought a book only to find out that 1/3rd of its size was reserved for a verbatim dump of the framework's javadoc.&Acirc;&nbsp; A waste of trees if you ask me.

After the first two parts of the book are concluded the authors take you into the advanced topics in Wicket and this really does show off the fact that the book was created with "real world" applications in mind.&Acirc;&nbsp; The <a href="http://manning.com/dashorst/ch08_dashorst.pdf">chapter on creating your own custom components</a> is definitely something any serious developer is going to have to tackle at one point or another.&Acirc;&nbsp; The go on to talk about topics such as authentication, localization (which is truely stellar in Wicket) and practical chapters on how to structure you application and what to do when you want to put it into production.&Acirc;&nbsp; All useful things!

Now obviously I'm a fan of Wicket and I'm a fan of this book, however if I had to knock the book in anyway it would be in that I think the authors should have stressed two points further than they did:

<ol>
<li>Wicket solves the disconnect between designer and developer that plauges most projects</li>
<li>How does Wicket scale?</li>
</ol>
To get a better sense of what I mean, let me explain:

1. I've been on projects where we have a great design team, they know Dreamweaver and Photoshop inside and out. They hand us html/css templates and we butcher them to death with jsp.&Acirc;&nbsp; If they ever want to change their design, they can't do it to the existing templates we now control because they don't understand jsp and jsp doesn't render truly in Dreamweaver.&Acirc;&nbsp; Wicket -- albeit not without basic training and convention use for both designers and developers -- offers the ability to get out of this hellish situation where designers and developers can't collaborate properly.

2. Scaling.&Acirc;&nbsp; This is where you'll get the fortune 500 to sign up to use Wicket.&Acirc;&nbsp; If you can explain to them concisely how Wicket can be made to scale I think it will set Wicket soaring.&Acirc;&nbsp; As you may or may not know, Wicket uses the Session heavily which could mean "large memory footprint" for larger apps with lots of pages.&Acirc;&nbsp; A chapter on Scaling would have been a great capper for this book, even if it were to dispel the Myth that Wicket won't scale on its own!

This being said, again, the book was a delight to read, their use of humour througout the book was good and I hope it turns others onto Wicket, mostly so I don't have to go to another client and convince them why it might be better to use than Struts 1.2.x ;)

For more information and sample chapters please see the <a href="http://manning.com/dashorst/">Wicket in Action landing page</a> over at Manning.

