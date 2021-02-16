---
layout: post
status: publish
published: true
title: OmniFocus and AppleScript and Next Actions
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2013-07-09T22:02:29.0000Z'
tags: []
comments: true
---
I've recently reignited and cleaned up my <a href="http://www.omnigroup.com/products/omnifocus/">OmniFocus</a> projects and tasks after reading a brilliant article about utilizing GTD contexts in a different way.  Sven Fechner over at Simplicity Bliss discusses <a href="http://simplicitybliss.com/2011/06/a-fresh-take-on-contexts/">"A Fresh Take on Contexts"</a>.  Go ahead and read it, it makes a whole lotta sense.  Our devices have made many of the contexts from GTD parlance unnecessary, as email and much of the office work can all be done in a caf&eacute;.  

<!--more-->

One of the gems in that blog post, was an AppleScript you could use to find all the projects that didn't have any Next Actions assigned.  Curt Clifton who works for The Omni Group wrote an AppleScript which does just that and with it you can <a href="http://www.curtclifton.net/projects/">Verify Next Actions Exist</a>.

Installed and brilliant, unless you have removed or have never installed Growl, and instead rely now on Notification Center in Mountain Lion.  Time for you to figure out that (* and *) are comments in AppleScript, and there are 3 places you'll need to find and fix.  Or you could use and install <a href="http://collect3.com.au/hiss/">Hiss</a> which will forward all Growl notifications to Notification Center.  Or... you can use this updated script that utilizes an Automator action to talk to Notification Center in lieu of Growl.

<ol>
<li>Download the <a href="http://www.automatedworkflows.com/2012/08/26/display-notification-center-alert-automator-action-1-0-0/">Display Notification Center Alert</a> automator action from Automated Workflows.  When downloaded unpack it and double click on the .action file</li>
<li>Create a new workflow in Automator and add the "Display Notification Center Alert" action.</li>
<li>After dragging the "Display Notification Center Alert" to the right pane, at the bottom add variables by right-clicking and selecting "New Variable" and using the names <em>title</em>, <em>subtitle</em>, and <em>message</em>.</li>
<li>Drag each variable to the corresponding field in Display Notification Center Alert action</li>
<li>Save the workflow as Display Notification.workflow.  Place it in <em>~/Library/Workflows.<em></li>
<li>Download this updated <a href="http://mysticweb-bucket.s3.amazonaws.com/scripts/Verify%20NAs.scpt">Verify NAs.scpt</a> and place in <em>~/Library/Scripts/Applications/OmniFocus</li>
</ol>
After that if you right-click on the toolbar and Customize Toolbar, you'll see a Verify NAs, drag it to your toolbar and close the customize pane.  Click it, and enjoy.

