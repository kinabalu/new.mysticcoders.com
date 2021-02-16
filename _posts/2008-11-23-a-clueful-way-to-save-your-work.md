---
layout: post
status: publish
published: true
title: A clueful way to save your work
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2008-11-23T03:07:11.0000Z'
comments: true
---
So, last night in haste I wrote a pensive rm -rf command which, if my previous find statement was correct, should have worked fine.

Well, it didn't.

There I was, with an empty directory, cursing every expletive I could think of. I broke down, and bought <a title="FileSalvage" href="http://subrosasoft.com/OSXSoftware/index.php?main_page=product_info&amp;products_id=1">FileSalvage</a>, making sure I had booted into the external drive, leaving the offending drive in a pristine condition. After $79.99 and a download and install behind me, the program is scanning for my long lost files.

It was going to take a while, so I left the office in a somewhat sour but hopeful mood. Then I remembered... <a title="DropBox" href="http://www.getdropbox.com/">DropBox</a>.

The night before my heinous misuse of the rm command, I had decided to symlink the directory to <a title="DropBox" href="http://www.getdropbox.com/">DropBox's</a> folder, and let it pick everything up. A quick visit to the website confirmed, files were removed, lots of them. And then the "Recover Files" option. Sweet victory.

After a quick reboot of the host machine, <a title="DropBox" href="http://www.getdropbox.com/">DropBox</a> dutifully put everything back, erasing the history of my fumbled find statement.

For you unix folks, here's the command for symlinking outside of the main folder:

<blockquote>ln -s /source/path/to/directory/or/file /destination/path/to/dropbox/folder</blockquote>

