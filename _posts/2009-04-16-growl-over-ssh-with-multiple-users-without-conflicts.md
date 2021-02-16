---
layout: post
status: publish
published: true
title: Growl over SSH with multiple users without conflicts
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-04-16T08:30:51.0000Z'
comments: true
---
In our previous post, we showed you how to use socat, ssh, and some fancy footwork to pipe a UDP-based request coming from <a href="http://search.cpan.org/~nmcfarl/Net-Growl-0.99/lib/Net/Growl.pm" title="Net::Growl" target="_blank">Net::Growl</a>, through a TCP-based reverse-proxy ala SSH, and back to the Mac as a UDP-request straight to <a href="http://growl.info" title="Growl" target="_blank">Growl</a>.

What happens if you've got more than one person wanting to do the same thing from that same shell server? Aside from everyone agreeing on a few ports they'd like to reserve for their Growl activities, here's the skinny.

<a id="more"></a><a id="more-771"></a>

<h2>The problem</h2>
When we first wanted to do this, the only technology hurdle, was that Net::Growl in its current incarnation does not support passing a unique UDP port in the register and notify calls. Not a big deal, here's what you do to fix:

<ol>
<li>Download <a href="http://search.cpan.org/~nmcfarl/Net-Growl-0.99/lib/Net/Growl.pm" title="Net::Growl" target="_blank">Net::Growl</a> distribution to your linux machine</li>
<li>Apply this <a href="http://www.mysticcoders.com/tools/downloads/net-growl.patch" title="Net::Growl patch to support UDP" target="_blank">patch</a> to the distribution</li>
<li>Rebuild it via: perl Makefile.PL, make, sudo make install</li>
</ol>
If you're using our super fancy <a href="http://www.mysticcoders.com/apps/growl-notify/" target="_blank">growl-net-notify.pl</a> script for <a href="http://weechat.flashtux.org" title="Weechat" target="_blank">Weechat</a>, it is already set up to pass a different UDP port by changing the settings in the /growl setup call.

<h2>Scenarios</h2>
For the TCP side of the equation, you'll each have to pick your own TCP port (above 1024 of course) to reverse-proxy with. In my previous example I chose 9999, so you can have others choose 9900, 9901, 9902, whatever flows off the tongue.

On the local side, socat and your ssh reverse-proxy will change, but using UDP 9887 will not. Growl is listening there.

On the linux side, where weechat + screen is running, we'll use whatever our agreed upon TCP port was, and pick our own UDP port to pass messages to from Weechat. This is easily modified from growl-net-notify using /growl setup [host] [pass] [port] where port is whatever your agreed Growl port is going to be. And setup socat to use your agreed upon TCP port, and agreed upon UDP port and do a /growl test hello, world!

<h2>Examples</h2>
A lot of times reading manuals, I skim through to read the examples, in man pages its usually near the bottom. I need to see the commands in action. So here is an example with a situation you're likely to run into.

2 people using the same host for IRC. For the sake of example, we'll use the names: Steve and Andrew.

Steve will use: TCP 9900 and UDP 9889

On the Mac side the commands he'll use:

<pre>
% socat TCP-LISTEN:9900,reuseaddr,fork UDP:127.0.0.1:9887 &amp;
% ssh myserver -R 9900:127.0.0.1:9900
</pre>
On the Linux side the commands he'll use:

<pre>socat UDP-LISTEN:9889,reuseaddr,fork TCP:127.0.0.1:9900 &amp;</pre>
And from within Weechat's growl

<pre>/growl setup localhost mypass 9889</pre>
Andrew will use the defaults: TCP 9999 and UDP 9887

<pre>socat TCP-LISTEN:9999,reuseaddr,fork UDP:127.0.0.1:9887 &amp;
ssh myserver -R 9999:127.0.0.1:9999
</pre>
On the Linux side the commands he'll use:

<pre>socat UDP-LISTEN:9887,reuseaddr,fork TCP:127.0.0.1:9999 &amp;</pre>
And from within Weechat's growl

<pre>/growl setup localhost mypass 9887</pre>
It's that simple.

