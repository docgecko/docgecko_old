---
layout: article
title: "Kill rails server"
categories: code
comments: true
ads: true
image:
  teaser: kill-server.jpg
---

This is just a brief reminder to myself of how to get rid of an already running rails server (for when it happens).

All you need to do in the console, within your application subdirectory is type:

<pre>ps aux | grep rails</pre>

After which you should see some output like this:

<pre>Daren    89314   0.2  2.0  2551940  82732 s003  R+    2:38PM   2:38.93 /Users/Daren/.rvm/rubies/ruby-1.9.2-p180/bin/ruby script/rails s
Daren     2881   0.1  0.1  2524112   3296 s005  R+   Wed06PM   2:11.62 /Users/Daren/.rvm/rubies/ruby-1.9.2-p180/bin/ruby script/rails c</pre>

The second line is my running server that I need to shutdown, which can be done as follows:

<pre>kill -9 #pid</pre>

In my case, the pid is 89314, so I use the following:

<pre>kill -9 2881</pre>

You can check if the server is down, by again typing in:

<pre>ps aux | grep rails</pre>

In my case, I would see something like this:

<pre>Daren     2881   0.1  0.1  2524112   3296 s005  R+   Wed06PM   2:11.62 /Users/Daren/.rvm/rubies/ruby-1.9.2-p180/bin/ruby script/rails c</pre>

i.e the rails server process has been killed!
