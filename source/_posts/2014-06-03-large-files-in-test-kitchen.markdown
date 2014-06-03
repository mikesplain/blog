---
layout: post
title: "Large files in test kitchen"
date: 2014-06-03 17:33:12 -0400
comments: true
categories: chef ruby devops
---

A quick tip when testing Chef cookbooks in Test Kitchen.  

Lets say you're spinning up a Tomcat app, so you create a run list with the Tomcat7 and the Java cookbooks.  Great it works!

Now you go to add features, and each destroy and converge taks a million years to download JDK.  You could setup a cache or something with polipio but there's a simpler way that Sean Carolan just shared with me:

Place the big files locally in a folder on your disk.  In your `.kitchen.yml` add a few configs:

```ruby
---
driver:
  name: vagrant

provisioner:
  name: chef_zero
  data_path: /Users/mike/Box\ Documents/private-chef

platforms:
  - name: ubuntu-12.04
    attributes:
      java:
        jdk:
          7:
            x86_64:
              url: file:///tmp/kitchen/data/jdk-7u55-linux-x64.tar.gz
