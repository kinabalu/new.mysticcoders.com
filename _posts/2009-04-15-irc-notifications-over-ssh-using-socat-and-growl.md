---
layout: post
status: publish
published: true
title: IRC notifications over SSH using socat and growl
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-04-15T09:30:18.0000Z'
comments: true
---
Here at Mystic, we do a lot of remote work using <a href="http://en.wikipedia.org/wiki/Internet_Relay_Chat" target="_blank">IRC</a>. And due to the heavy use of best of breed tools under OSX and Linux, we find ourselves working in a shell a lot of the day, along with our IDE. We like to keep in the Flow state for as much of the time working as possible, so we turn off things like email notifications, twitter popups, and other such annoyances.

<a id="more"></a><a id="more-756"></a>

Since our communication is important, but we don't want it to be distracting, going back and forth to an IRC window became, tedious. After searching high and low, we have what appears to be a pretty nice solution to the problem at hand. It uses SSH tunneling, <a href="http://www.dest-unreach.org/socat/doc/socat.html" target="_blank">socat</a>, <a href="http://growl.info" target="_blank">Growl</a> for notifications, <a href="http://www.gnu.org/software/screen/" target="_blank">screen</a> for staying online, and <a href="http://weechat.flashtux.org" target="_blank">Weechat</a> as the irc client. Any client that uses <a href="http://search.cpan.org/~nmcfarl/Net-Growl-0.99/" target="_blank">Net::Growl</a> should perform adequately, Weechat isn't a requirement, just the console-based irc client we prefer.

<h2>Initial setup</h2>

A couple of required items before we begin:

<ul>
<li>A Mac with Growl installed (the basics would have to be adapted for growl-like notifiers in linux and windows)</li>
<li>An unix-based outside shell account with ssh access and Weechat (or irssi) installed</li>
</ul>
<h2>Setting up the Mac side</h2>
<h3>Growl</h3>

If you don't have Growl already installed on your Mac, <a href="http://growl.info" target="_blank">Get it!</a> Open System Preferences and select Growl from under the "Other" panel. After Growl's preference pane comes up, select the Network tab.

<img src="https://www.mysticcoders.com/wp-content/uploads/2009/04/picture-1.png" width="480" height="403" alt="Growl Network Tab" />

What you'll need checked here, is "Listen for incoming notifications" and "Allow remote application registration" along with entering in your super-secret password.

<h3>socat on the Mac</h3>

First thing we're going to assume, is that you have <a href="http://www.macports.org/" target="_blank">MacPorts</a> installed. Ready? If it's not already installed (it probably won't be), open up Terminal and execute:

<pre>sudo port install socat</pre>
Follow along the prompts to get that installed. And with that Terminal open, assuming you'll be using TCP port 9999 to reverse-proxy the Growl data, execute:

<pre>socat TCP-LISTEN:9999,reuseaddr,fork UDP:127.0.0.1:9887 &amp;</pre>
What this does, is setup a forward so that any packets hitting TCP port 9999 will proxy to UDP port 9887 (the magic Growl UDP port). I would suggest, that you setup this command as an alias in your ~/.bashrc or similar for your shell.

Now we'll setup our reverse-proxy with SSH and our shell that we run Weechat on:

<pre>ssh myserver -R 9999:127.0.0.1:9999</pre>
What this command says is, setup a reverse proxy with myserver so all traffic on TCP port 9999 is transmitted and received over our secure SSH connection. We're also assuming with this command, that you've set up SSH certificate authentication, which is really handy for logging into any unix-based servers with SSH.

<h2>Setting up the Linux side</h2>
<h3>socat on Linux</h3>


Since there are many many flavors of Linux, I'll tailor the discussion assuming whatever distro you're using, it has apt-get or similar. As with the Mac, we do a simple

<pre>apt-get install socat</pre>
Follow along the prompts to get that installed. And now we'll setup socat to proxy in the opposite direction

<pre>socat UDP-LISTEN:9887,reuseaddr,fork TCP:127.0.0.1:9999 &amp;</pre>
And this will listen for UDP connections on 9887 and proxy those through TCP 9999. If you're really astute, you'll realize, that we've just completed the networking end-to-end. And again, I would set the above socat command up in a .bashrc or similar.

<h2>Weechat Setup</h2>
When we run Weechat, we like to always be on, which is the biggest reason for having it running in a Linux shell. To that end, we have screen running on the server with Weechat always-on. This is quite simple, after logging into your linux shell:

<pre>
% screen
% weechat-curses
</pre>

When we're not on the shell or in irc, we detach from that screen session using Ctrl-A + D and exit. Our IRC session is always running, and we can get back to it with:

<pre>screen -r</pre>
If you have multiple screens, or accidentally killed your shell without properly detaching, we're going to assume that you can <strong>man screen</strong> and read.

We took many clues and a lot of sample code from <a href="http://weechat.flashtux.org/scripts/growl-notify.pl" target="_blank">growl-notify</a>, modified it heavily and made changes to support Net::Growl. So the next step is to head over <a href="/apps/growl-notify/">here and download growl-net-notify.pl</a> into your ~/.weechat/perl or ~/.weechat/perl/autoload directory.

<strong>[EDIT] </strong>Now wait a minute, maybe you're not groking what Net::Growl is, and the other dependency, <a href="http://search.cpan.org/~bingos/Parse-IRC-1.12/lib/Parse/IRC.pm" target="_blank">Parse::IRC</a>.  No problem, just make sure you have perl installed, and type:

<pre>
% cpan -i Net::Growl
% cpan -i Parse::IRC
</pre>
If this is your first time using cpan, you'll have a bunch of questions to answer, most of the defaults in here are okay.

After you've loaded the script, you'll need to run the setup:

<pre>/growl setup [host] [password] [port]</pre>
You'll have to enter <em>at least</em> <strong>host</strong> and <strong>password</strong>, <strong>port</strong> is already the default 9887. To test that it works, use the <strong>/growl test [message]</strong> command.

Let us know either on IRC or via our <a href="/contact">Contact page</a> if everything worked out, if you have any questions or feature requests.

Have fun! And get back to working in Flow!

A big thanks goes out to:

Conky on irc.freenode.net #basementcoders - the socat over ssh underpinnings originated with him
sf11 and jgenender on irc.freenode.net - early testing on the script was a great help

