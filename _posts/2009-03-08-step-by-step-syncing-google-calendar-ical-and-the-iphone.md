---
layout: post
status: publish
published: true
title: Step-by-Step Syncing Google Calendar, iCal and the iPhone
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-03-08T13:30:23.0000Z'
tags: []
comments: true
---
I spent some time yesterday working with the new <a href="http://caldav.calconnect.org/" title="CalDAV" target="_blank">CalDAV</a> support in <a href="http://calendar.google.com/" title="Google Calendar" target="_blank">Google Calendar</a>. If you've been under a rock, they announced support for it very recently, and for users of the <a href="http://www.apple.com/iphone/" title="Apple iPhone" target="_blank">iPhone</a>, and users of iCal, it means seamless syncing of their Google Calendar's with all 3 systems. What I found lacking, was a step-by-step tutorial on setting everything up, and the pitfalls and hidden steps along the way. First, a few assumptions to go over:


<ol>
<li>You've got a Gmail account or have a domain with Google Apps</li>
<li>You've got a Mac with iCal <em>(only required if you want the iCal sync with Google Calendar)</em></li>
<li>You've got an iPhone <em>(only required if you want to sync your iPhone with Google Calendar)</em></li>
</ol>
<a id="more"></a><a id="more-342"></a>

<h1>Step 1 - Setup Google Calendar</h1>Google has created a great system for managing calendars and sharing them with others across a domain, or individually with their Gmail account. The initial setup will involve having the default calendar setup with your account. If you have multiple calendars, continue to set those up as you normally would and we'll show you how to get those sync'd with iCal and iPhone.

Google Apps users will need to ensure that the administrator for their domain logs into the management website, clicks Service Settings and Mobile in the menu. On that page click the checkbox for "Enable Google Sync" and hit "Save Changes".

<div style="text-align: left;">
  <img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/picture-3.png" width="480" height="188" alt="Picture 3.png" />
</div>

<h1>Step 2 - Setup iCal</h1>In setting up iCal, we can do this the easy way, or the hard way. As long as their is no difference in the effectiveness of the end result, I choose easy. To that end, we have <a href="http://code.google.com/p/calaboration/" title="Calaboration" target="_blank">Calaboration</a>, a Google Sync setup tool specifically for setting up the CalDAV links in iCal.

1. <a href="http://code.google.com/p/calaboration/downloads/list" title="Download Calaboration" target="_blank">Download Calaboration</a> 2. Copy the .app file to the Applications/ folder on your Mac
<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/picture-11.png" width="152" height="137" alt="Picture 1.png" />

3. Run the Calaboration.app
<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/picture-4.png" width="466" height="183" alt="Picture 4.png" />

4. Sign in with your Gmail or Google Apps login. Full email address with the password.
<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/picture-7.png" width="480" height="223" alt="Picture 7.png" />
5. If iCal is already running, the application will prompt you to quit iCal and return to the application for setting up your calendars.
<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/picture-5.png" width="460" height="480" alt="Picture 5.png" />
6. Select from the listed calendars what you would like to sync7. Click Add to iCal<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/picture-6.png" width="480" height="224" alt="Picture 6.png" />
<em>NOTE: If you see your calendars greyed out, they've already been added, if they are greyed out with a lock to the right, they are read-only. If you want to add them to iCal, visit the applications preferences and select the "Enable read-only calendars" option. Make sure to read the message that goes along with this option, as it could affect your use of iCal.</em>

8. Okay, all done with that? Awesome. You're done with the iCal setup.<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/picture-8.png" width="480" height="223" alt="Picture 8.png" />
<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/picture-9.png" width="179" height="179" alt="Picture 9.png" />

9. Open iCal and ensure that each of the calendars selected from Calaboration show up. You'll notice that instead of the sane method, of having all calendars show under the same CalDAV server, the current implementation has one for each.

10. iCal is now set-up and syncing with Google. Try it. Go wild. Add stuff and see it magically popup in iCal. Remove stuff, refresh Google Cal and see it disappear. Magic.

<span style="font-size: 24px; font-weight: bold;">Step 3 - Setup iPhone</span>

<strong>IMPORTANT: If there is anything on your iPhone that isn't sync'd with your computer, plug in your iPhone and sync now. The process of setting up the iPhone deletes all calendars in order to proceed.</strong>


The next step is covered very well in Google Support and should be followed completely: <a href="http://www.google.com/support/mobile/bin/answer.py?answer=138740" title="Sync: Set Up Your iPhone or iPod Touch" target="_blank">Sync: Set Up Your iPhone or iPod Touch</a>


If you are using Gmail, you are done. If you are using Google Apps, and you have multiple calendars to sync, follow along for the conclusion.


<strong>Getting Started</strong>


1. Visit http://m.google.com/sync


2. You'll see a webpage with icons for the various Google services, scroll down till you see:



<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/img-0005.png" width="192" height="288" alt="IMG_0005.PNG" /> <img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/img-0006.png" width="192" height="288" alt="IMG_0006.PNG" />


3. Tap the rounded button below the icons to configure sync for your domain


4. In the popup, enter the domain name for your Google App account, and press the Go button to the right of the text field. Do not use the iPhone Go button at the bottom right, as it only refreshes the page. Details on why this doesn't work would be boring.



<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/img-0002.png" width="192" height="288" alt="IMG_0002.PNG" />&nbsp;&nbsp;<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/img-0003.png" width="192" height="288" alt="IMG_0003.PNG" />&nbsp;&nbsp;<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/img-0004.png" width="192" height="288" alt="IMG_0004.PNG" />


5. You will see an interface like the one in the first picture, select "iPhone".


6. Here you'll see a list of possible calendars to sync with the iPhone calendar. You'll see that the default calendar is already selected, simply select the calendars you want, and click "Save".


7. After hitting "Save" you will see a confirmation page which notifies of success.


8. That's it. Hit the "Home" button on your iPhone and select the Calendar. Within a few moments your Calendar items should stream into multiple calendars on the device.


I hope this quick tutorial was informative. The steps are fairly simple, but I was unable to find a tutorial that brought each of the pieces and caveats of each step together. Happy syncing!


