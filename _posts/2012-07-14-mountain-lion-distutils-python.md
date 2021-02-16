---
layout: post
status: publish
published: true
title: Mountain Lion GM, distutils, and Python
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2012-07-14T23:50:05.0000Z'
tags: []
comments: true
---
Since installing the GM of a new cat, there have been relatively few issues.  Thankfully.

One that recently came up, was attempting to run nosetests in a virtualenv using Python.  The oddest error showed after running nose, pip, etc

<code>
Traceback (most recent call last):
  File "~/.env/virtualenv/bin/pip", line 5, in <module>
    from pkg_resources import load_entry_point
  File "~/.env/mailup/lib/python2.7/site-packages/setuptools-0.6c11-py2.7.egg/pkg_resources.py", line 698, in <module>
  File "~/.env/mailup/lib/python2.7/site-packages/setuptools-0.6c11-py2.7.egg/pkg_resources.py", line 701, in Environment
  File "~/.env/mailup/lib/python2.7/site-packages/setuptools-0.6c11-py2.7.egg/pkg_resources.py", line 96, in get_supported_platform
  File "~/.env/mailup/lib/python2.7/site-packages/setuptools-0.6c11-py2.7.egg/pkg_resources.py", line 221, in get_build_platform
  File "~/.env/mailup/lib/python2.7/distutils/__init__.py", line 16, in <module>
    exec(open(os.path.join(distutils_path, '__init__.py')).read())
IOError: [Errno 2] No such file or directory: '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/distutils/__init__.py'
</code>

Further inspection revealed that the Python lib folder didn't include any source files.  And the easiest way to fix this, was to go to the <a href="http://developer.apple.com">Apple developer site</a> and install the "Command Line Tools for Xcode 4.5 Developer Preview 2 (OS X Mountain Lion)".  

Problem solved.

