---
layout: post
status: publish
published: true
title: Setting up your Apache Wicket project with Maven
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2010-12-01T16:21:48.0000Z'
tags: []
comments: true
---
Here at Mystic, we love <a href="http://wicket.apache.org" target="_blank">Apache Wicket</a> for it's clean separation of mark-up and logic, simple POJO data model and one of the first web frameworks to espouse lack of XML as a benefit.

1. Download <a href="http://maven.apache.org">Apache Maven</a>.
2. Install it and ensure that the mvn executable is in your path.
3. Run the following command to download and execute the mvn archetype for Wicket:

<pre>mvn archetype:create \
-DarchetypeGroupId=org.apache.wicket \
-DarchetypeArtifactId=wicket-archetype-quickstart \
-DarchetypeVersion=1.4.14 \
-DgroupId=com.mycompany \
-DartifactId=myproject</pre>
That's it.  This creates the Maven directory structure with the appropriate pom.xml setup and the dependencies in your ~/.m2 directory.  After this you should be ready to rock your next <a href="http://wicket.apache.org" target="_blank">Apache Wicket</a> application.

