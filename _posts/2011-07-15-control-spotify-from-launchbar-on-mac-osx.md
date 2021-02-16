---
layout: post
status: publish
published: true
title: Control Spotify from Launchbar on Mac OSX
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2011-07-15T17:13:39.0000Z'
tags: []
comments: true
---
<img src="https://www.mysticcoders.com/wp-content/uploads/2011/07/spotify.jpg" border="0" />

<strong>EDIT:</strong> <em><a href="http://www.jacktams.net/2011/07/21/spotify-applescripts-0-5-2/">Follow this link</a> for the latest scripts which utilize Spotify's more enhanced Applescript support</em>

If you've been following the latest invite craze, the latest one has to be for the European import <a href="http://www.spotify.com">Spotify</a>.  Basically millions of tracks you can play instantly on your computer for free + ads, and for a fee they'll remove the ads, and an additional fee they'll let you stream their catalog from your mobile phone (iPhone or Android).  I don't have any more invites, but I know a lot of folks have had success putting their email in for <a href="https://www.spotify.com/us/amanda/">@amanda</a>.  And of course, <a href="https://www.spotify.com/us/download/mac/">visit here to download the Spotify client</a>.

If you're a user of <a href="http://www.obdev.at/products/launchbar/index.html">Launchbar</a> or Quicksilver, these Applescripts (for Launchbar) placed in <code>~/Library/Application Support/LaunchBar/Actions/Spotify</code> should help in controlling your music playing without having to constantly open Spotify.   For updates and the original author of these scripts <a href="http://www.jacktams.co.uk/2010/04/28/spotify-applescripts-version-0-4-3/">visit this blog article</a>.  After adding these applescripts activate Launchbar and select the Index menu > Update Index > All Rules.  Or just activate Launchbar and press Cmd-0.

<h2>Spotify - Next Song</h2>

``` applescript
tell application "System Events"
	set MyList to (name of every process)
end tell

tell application "System Events" to set appList to Â¬
	name of application processes whose frontmost is true

set activeApp to item 1 of appList
if (MyList contains "Spotify") is true then
	tell application "Spotify" to activate
	tell application "System Events"
		tell process "Spotify"
			click menu item 3 of menu 1 of menu bar item 6 of menu bar 1
		end tell
	end tell
	tell application "System Events"
		set visible of process "Spotify" to false
	end tell
end if
```

<h2>Spotify - Previous Song</h2>

``` applescript
tell application "System Events"
	set MyList to (name of every process)
end tell

tell application "System Events" to set appList to Â¬
	name of application processes whose frontmost is true

set activeApp to item 1 of appList
if (MyList contains "Spotify") is true then
	tell application "Spotify" to activate
	tell application "System Events"
		tell process "Spotify"
			click menu item 4 of menu 1 of menu bar item 6 of menu bar 1
		end tell
	end tell
	tell application "System Events"
		set visible of process "Spotify" to false
	end tell
end if
```

<h2>Spotify - Play/Pause</h2>
``` applescript
tell application "System Events"
	set MyList to (name of every process)
end tell

tell application "System Events" to set appList to Â¬
	name of application processes whose frontmost is true

set activeApp to item 1 of appList
if (MyList contains "Spotify") is true then
	tell application "Spotify" to activate
	tell application "System Events"
		tell process "Spotify"
			click menu item 1 of menu 1 of menu bar item 6 of menu bar 1
		end tell
	end tell
	tell application "System Events"
		set visible of process "Spotify" to false
	end tell
end if
```

<h2>Searching in Spotify</h2>
Searching is vital to using this new tool for us folks over here in the United States, and our European brethren have been busy with helpers like <a href="http://blindrocket.blogspot.com/2009/09/searching-spotify-with-quicksilver.html">searching Spotify with Quicksilver</a>.  I liked this so much, I've adapted the instructions for Launchbar:

<ol>
<li>Activate Launchbar and select Index > Show Index or Option+Cmd+I</li>
<li>Browse to Search Templates (UTF-8) and click Add</li>
<li>Add the following entries:
<ul>
<li>Name: <code>Search Spotify for Album</code> Detail: <code>spotify:search:album:*</code></li>
<li>Name: <code>Search Spotify for Artist</code> Detail: <code>spotify:search:artist:*</code></li>
<li>Name: <code>Search Spotify for Song</code> Detail: <code>spotify:search:title:*</code></li>
</ul>
</li>
<li>Update Index for Search Templates (UTF-8) or All</li>
</ol>
<h2>If using Spaces</h2>
Some folks have reported that when using Spaces in OSX the Applescripts have switched to that Space.  Simplest thing to do is set the preferences for Spaces so that Spotify shows up on "Every Space".

Enjoy the music!

<strong>EDIT:</strong> <em><a href="http://www.jacktams.net/2011/07/21/spotify-applescripts-0-5-2/">Follow this link</a> for the latest scripts which utilize Spotify's more enhanced Applescript support</em>
