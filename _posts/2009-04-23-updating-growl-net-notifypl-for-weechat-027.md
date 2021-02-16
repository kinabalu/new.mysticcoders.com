---
layout: post
status: publish
published: true
title: Updating growl-net-notify.pl for Weechat 0.2.7
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-04-23T09:00:17.0000Z'
comments: true
---
Humans love shiny. And there's nothing more shiny to a programmer than a brand new version of a tool used every day. Here at <a href="http://www.mysticcoders.com">Mystic</a> we use IRC as our primary mode of communication and project collaboration, and several of us have chosen <a href="http://weechat.flashtux.org" title="Weechat" target="_blank">Weechat</a>. Recently we wrote about receiving <a href="http://www.mysticcoders.com/blog/2009/04/15/irc-notifications-over-ssh-using-socat-and-growl/" title="IRC Notifications over SSH using socat and growl" target="_top">irc notifications from Weechat over ssh</a>, and the plugin written was for the current stable version of <a href="http://weechat.flashtux.org/download.php" title="Weechat 0.2.6 download" target="_blank">Weechat 0.2.6</a>. The development version of Weechat is a complete rewrite, and breaks plugin compatibility. If we rewrote <a href="http://www.mysticcoders.com/apps/growl-notify/" title="growl-net-notify plugin" target="_blank">growl-net-notify.pl</a> for this new version, the choice was to manage two separate source files, or find some way to make one work for both. I chose the latter method because programmers as I've always said, are smart lazy people, we'll do whatever we can to automate something.

<a id="more"></a><a id="more-798"></a>

<h2>Identifying Weechat version</h2>
First step in combining the version code is identifying which version we're running under. Since I don't want to cause any errors in either version when the plugin is loaded, I decided to use Perl's <em>exists</em> method which can be used to check existence of a subroutine:

<pre>if(exists &amp;weechat::info_get)</pre>
With this in hand, we can perform the setup commands for 0.2.6 and 0.2.7 independent of each other, and preset the <em>$weechat_version</em> variable so we can key off of it in other portions of the code.

<h2>Changes in 0.2.7</h2>
Notifications to the user are prevalent throughout our code, we have several easy commands so the user can easily setup their network settings and parameters that are important. So we use <em>weechat::print</em> a lot, and it has changed in 0.2.7.

The previous version gave you a single parameter for <em>weechat::print</em>, and output to whatever your active buffer was. The new version forces you to specify a buffer, and allows you to pass in an empty string to denote the main buffer.

<em>weechat::print(message)</em> becomes <em>weechat::print(buffer, message)</em>

The developers have standardized on different naming conventions for 0.2.7, and a lot of action oriented items are hook's. So instead of <em>weechat::add_command_handler</em>, you would write <em>weechat::hook_command</em>. There were some subtle parameter changes as well, and since 0.2.7 is not final, this still might change.

<em>weechat::register</em> has also seen some minor changes, including parameters for author, and license type.

Purely cosmetic changes it seems, <em>weechat::get_plugin_config</em> becomes <em>weechat::config_get_plugin</em>, and <em>weechat::set_plugin_config</em> becomes <em>weechat::config_set_plugin</em>.

The status constants have also changed, so <em>weechat::PLUGIN_RC_OK</em> becomes <em>weechat::WEECHAT_RC_OK</em>, and <em>weechat::PLUGIN_RC_KO</em> becomes <em>weechat::WEECHAT_RC_ERROR</em>.

<h2>Message Handling</h2>
The majority of the work done in upgrading for 0.2.7 came at the hands of the public message highlights, and the private message highlights. In the previous version, we used <a href="http://search.cpan.org/~bingos/Parse-IRC-1.12/" title="Parse::IRC" target="_blank">Parse::IRC</a> to pull out the pieces of the IRC message that were important to us to show in our notification. In 0.2.7, they've simplified things for us greatly.

<em>weechat::hook_print</em> allows us to watch each message, check if our nick has been highlighted, and process accordingly. We're passed in a nice array which gives us if we're highlighted, who said it, and the message.

<em>weechat::hook_signal</em> allows us to watch private messages, and gives us a string separated by a tab with the nick that is private messaging, and the message. We might have liked it if we got a nice fancy array passed in just like hook_print gives us, but this works out just fine.

Because we were writing this for both 0.2.6 and 0.2.7, I wanted to get rid of the dependency on Parse::IRC and put a little bit of Perl regex magic to work. Together armed with <a href="http://www.regextester.com" title="RegexTester.com" target="_blank">RegexTester.com</a>, I was able to pull out the data needed in these legacy methods and achieve detachment from needing Parse::IRC.

Now I'm sure there are some Perl-style tricks that I'm not doing here, and the code definitely fails because it is readable :). And of course, after talking with FlashCode on #weechat, he suggested we continue development only on 0.2.7, so the version in source control has two versions now. Feel free to look at older commits to see the combined version, it was definitely fun to figure out how to write it.

If you'd just like to use this plugin for your <a href="http://growl.info" title="Growl" target="_blank">Growl</a> notification over SSH needs:

<a href="http://www.mysticcoders.com/apps/growl-notify/" title="growl-net-notify plugin" target="_blank">Download the plugin</a>

If you're also interested in reviewing the source:

<a href="http://github.com/kinabalu/weechat-plugins/tree/master" title="GitHub Repository for Weechat Plugin" target="_blank">GitHub repository here</a>

