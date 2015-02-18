---
layout: article
title: "Upgrading and downgrading your rubygems"
categories: articles
comments: true
image:
  teaser: ruby-gems.jpg
---

I just upgraded to rubygems version 1.8.1, which generates a large number of depreciation errors.  This is not a major problem as all still continues to work fine, expect for a couple of my gems: the sunspot (1.2.1) and sunspot_rails (1.2.1) gem.  Currently, under gem 1.8.1, I was unable to start or reindex solr.

As such, for now I decided to revert back to version 1.7.2 of rubygems.  In order to downgrade your rubygems version, you have to do the following:

<pre>gem uninstall rubygems-update -v 1.8.1
gem install rubygems-update -v 1.7.2
update_rubygems _1.7.2_</pre>

And, going the other way, if you wish to update to the latest version, simply run:

<pre>gem update --system</pre>
