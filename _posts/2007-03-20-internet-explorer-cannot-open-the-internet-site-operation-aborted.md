---
layout: post
status: publish
published: true
title: Internet Explorer cannot open the Internet site. Operation aborted.
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2007-03-20T02:37:55.0000Z'
comments: true
---
Just got hit with this error:

<a href="http://www.mysticcoders.com/wp-content/uploads/2007/03/Picture%201-1.png"><img src="https://www.mysticcoders.com/wp-content/uploads/2007/03/Picture%201-1-tm.jpg" border="1" alt="Picture 1-1" hspace="4" vspace="4" width="373" height="100" /></a>

And regrettably I spent a lot of time searching through Javascript in <a href="http://www.mootools.net">mootools</a> javascript, and the small 2 lines of javascript <a href="http://www.wicketframework.org">Wicket</a> adds to the page.  Finally, I decided to actually google the error (which I should have done immediately).

Low and behold, because I was inlining some Javascript for the <a href="http://www.mootools.net">mootools</a> Tip plugin, Internet Explorer 6 and 7 were upset because I hadn't given them the chance to render the entire HTML yet.  Easy enough to fix, and actually cleans up the code real nice, was to either add it to the body onload, or use javascript.  In my case I used <a href="http://www.mootools.net">mootools</a> goodness:

<em>window.addEvent('domready', Site.start);
</em>
Gotta love Google.  <strike>You can also <a href="http://www.ryangrant.net/archives/internet-explorer-cannot-open-the-internet-site-operation-aborted-google-map-api">check this site</a> for some tips on avoiding the problem in the future (especially with GMaps).</strike>

<strong>UPDATE</strong> The site above seems to no longer be around, such is life.  I've posted a quick update for several javascript libraries on how to ensure you are executing javascript <a href="/blog/2009/07/11/loading-javascript-on-your-page-properl/">when the DOM is finished loading</a>.

