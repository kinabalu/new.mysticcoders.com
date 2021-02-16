---
layout: post
status: publish
published: true
title: Maven assembly plugin and Spring
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-03-02T10:30:43.0000Z'
tags: []
comments: true
---
Here at <a title="mystic coders - to our success!" href="http://mysticcoders.com/">Mystic</a>, we do a lot of different types of development. It's not all web development. I found myself writing a simple document parser that takes files uploaded by automated process, processes them by adding to a database, and finishes. One of the components we used to tie everything together, the <a title="Spring Framework" href="http://www.springframework.org" target="_blank">Spring Framework</a>, it's great for that.

Since this would be a command line app running via cron most likely, we pulled <a title="Apache Maven" href="http://maven.apache.org" target="_blank">Maven</a> and the <a title="Apache Maven assembly plugin" href="http://maven.apache.org/plugins/maven-assembly-plugin/" target="_blank">assembly plugin</a> from our bag-of-tricks. Simply adding something like so:

``` xml
<plugin>
              <artifactId>maven-assembly-plugin</artifactId>
              <configuration>
                  <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                  </descriptorRefs>
                  <archive>
                    <manifest>
                      <mainClass>com.your.main.class.file</mainClass>
                    </manifest>
                  </archive>
              </configuration>
            </plugin>
```

All was right with the world, until we ran it from the dependency included jar on the server, and BAM!

<span style="font-family: Arial;"><em>Exception in thread "main" org.springframework.beans.factory.parsing.BeanDefinitionParsingException: Configuration problem: Unable to locate Spring NamespaceHandler for XML schema namespace</em> <span class="nobr"><a href="http://www.springframework.org/schema/context"><em>http://www.springframework.org/schema/context</em></a></span></span>

<span style="font-family: Arial;">Ouch, how frustrating, this worked in the IDE "Works on my machine &acirc;&bdquo;&cent;". After a bit of googling I realized, we were using separate dependencies for the Spring jars that we needed, and the contents of META-INF/ were getting overwritten (only a single JAR remember?). So the solution was to swap out all the separate Spring dependencies in <a title="Apache Maven" href="http://maven.apache.org" target="_blank">Maven</a>, and use the all-in-one like so:</span>

``` xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring</artifactId>
            <version>${spring.version}</version>
        </dependency>
```
