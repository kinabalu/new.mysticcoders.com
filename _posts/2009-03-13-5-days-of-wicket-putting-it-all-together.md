---
layout: post
status: publish
published: true
title: 5 Days of Wicket - Putting it all together
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-03-13T09:00:39.0000Z'
tags: []
---
Here we are, the last day of our [Apache Wicket](http://wicket.apache.org) series. While there was only one day that focused on [Apache Wicket](http://wicket.apache.org), we've laid the groundwork needed to get a J2EE project that uses a web framework off the ground. And step-by-step, if you follow along with the team on all 4 days preceding, you will have a greater understanding of how everything is put together, and why.

And as you can see, the actual work that went into the front-end of <em>MysticPaste</em>, didn't take very long at all. And as good community citizens, we've made the finished product and source code available.

<!--more-->
[MysticPaste.com](http://www.mysticpaste.com) - our implemented pastebin based on the code examples and tutorials shown throughout this series

[MysticPaste Source](http://kenai.com/projects/mystic-apps) - the source code for the pastebin, open source.

[**On Day 1**](/blog/5-days-of-wicket-day-1), we learned the many steps it takes to put together a project that can be worked on in a team environment. We've enjoyed your comments on different methodologies, and some we will definitely take into account. Â The biggest thing to take away from that day, is to understand the underpinnings of why things are where they are in a project, and how adherence to the fairly accepted [Maven](http://maven.apache.org) standard structure can make life much much easier.

[**On Day 2**](/blog/5-days-of-wicket-writing-the-tests), we ran through some basics on Mocks, and with a bit of upfront design on interfaces, a testing harness is available for building out the backend. Â We also learned how to take [Unitils](http://www.unitils.org/) and extend our tests passed just a functional unit, and move through many layers of the built system, and ensure we go a bit beyond just code coverage.

[**On Day 3**](/blog/5-days-of-wicket-day-designing-the-backend), with unit tests in place, we felt safe writing some implementations so we moved the codebase from failing tests due to no implementation, to working tests. Â We learned a bit about designing your domain model based on requirements and design discussions, and molding the service and persistence layer to support your business goals.

[**On Day 4**](/blog/5-days-of-wicket-the-ui), we got to the most exciting part of our journey, [Apache Wicket](http://wicket.apache.org). The article walked you through some of the basics of putting a page together using markup inheritance, and amazingly enough, how this simple act removes the need for technology so common in the MVC world to support this. Best of all, because its all in Java, ultimately you can actually use your IDE and refactor or debug as needed. Each of the most basic components with forms, and display, and the wicket-based tags that act as extensions to your HTML pages, were reviewed with links off to the Javadoc for further discovery. One of the many reasons to love Wicket, is the clear separation of functional concerns, no code in your template pages, it's just HTML.

If you're like the members of the [Mystic](https://mysticcoders.com) team, you will CRAVE more. More information, more discovery into how we can fully integrate [Apache Wicket](http://wicket.apache.org) as a tool in our arsenal. Aside from downloading the distribution and following along with our tutorial, here are some options ahead:

1. **TSSJS / LV** - <a href="http://javasymposium.techtarget.com/html/frameworks.html#ALombardiWicket" target="_blank">Architecting Applications using Apache Wicket</a> - A talk at the symposium by Andrew Lombardi about Apache Wicket.
2. **Apache Wicket Training** - Mystic is available for corporate trainings, and is in process of putting together a schedule for different technologies to learn about in 2009. If you'd like to have us come to your business, have your manager contact us at: <a href="mailto:tr&#97;&#105;&#110;&#105;&#110;&#103;s&#64;m&#121;&#115;&#116;&#105;&#99;&#99;&#111;&#100;&#101;&#114;&#115;&#46;co&#109;">&#116;&#114;&#97;&#105;n&#105;&#110;&#103;&#115;&#64;&#109;&#121;&#115;&#116;&#105;c&#99;&#111;d&#101;rs&#46;&#99;&#111;m</a>

### What's next?
With the concepts in these articles, we've laid the groundwork for many more short articles on interesting technology in the future. There is definitely a lot of new and interesting items we can add to [MysticPaste](http://www.mysticpaste.com) without overcomplicating the streamlined interface, such as:

- Syntax Highlighting
- Embedding AJAX where it makes sense
- Remote Interface to the API for pasting from IDE's
- IRC-based bot integration
- Thoughts?  What other things would you like to see us cover on the blog?  Drop us a line at: <a href="mailto:&#116;&#97;&#108;&#107;&#64;mys&#116;&#105;cc&#111;&#100;&#101;&#114;&#115;&#46;&#99;&#111;&#109;">&#116;&#97;l&#107;&#64;&#109;&#121;&#115;&#116;&#105;&#99;c&#111;der&#115;&#46;&#99;&#111;&#109;</a>