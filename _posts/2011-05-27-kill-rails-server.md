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

{% highlight bash %}
ps aux | grep rails
{% endhighlight %}

After which you should see some output like this:

{% highlight bash %}
Daren    89314   0.2  2.0  2551940  82732 s003  R+    2:38PM   2:38.93 /Users/Daren/.rvm/rubies/ruby-1.9.2-p180/bin/ruby script/rails s
Daren     2881   0.1  0.1  2524112   3296 s005  R+   Wed06PM   2:11.62 /Users/Daren/.rvm/rubies/ruby-1.9.2-p180/bin/ruby script/rails c
{% endhighlight %}

The second line is my running server that I need to shutdown, which can be done as follows:

{% highlight bash %}
kill -9 #pid
{% endhighlight %}

In my case, the pid is 89314, so I use the following:

{% highlight bash %}
kill -9 2881
{% endhighlight %}

You can check if the server is down, by again typing in:

{% highlight bash %}
ps aux | grep rails
{% endhighlight %}

In my case, I would see something like this:

{% highlight bash %}
Daren     2881   0.1  0.1  2524112   3296 s005  R+   Wed06PM   2:11.62 /Users/Daren/.rvm/rubies/ruby-1.9.2-p180/bin/ruby script/rails c
{% endhighlight %}

i.e the rails server process has been killed!
