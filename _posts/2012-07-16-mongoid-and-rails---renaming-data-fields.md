---
layout: article
title: "Mongoid and Rails - Renaming data fields"
categories: articles
comments: true
ads: true
image:
  teaser: mongoid-rails.jpg
---

I have been recently upgrading a luxury property rentals website that I have been developing with a partner of mine.  The site is Ruby on Rails based and uses the awesomeness of the NoSQL Mongo (using the Mongoid gem).

Changes to the site included not only adding fields to a Collection (similar to a Table of a SQL type database), but the renaming of fields in an existing production database.

## So how to change existing field names?

Fortunately, this is actually quite simple.

Firstly, begin by logging into the rails console of your site.  I use RVM so I always start my rails terminal commands with:

<pre>bundle exec</pre>

As such, to log into the rails console, the full command is:

<pre>bundle exec rails c</pre>

In my case, I had a collection called "Photo" with a field called "order_no", which I began to dislike...lol.  Anyway, I wanted to change it to "position".  (This field was used to know the position or order of photos related to a property).

So, to make the field name change, within the terminal you use the "db.collection.update" command as follows:

<pre>db.collection.update( criteria, objNew, upsert, multi )</pre>

with the following arguments:

*criteria* - query which selects the record to update;
*objNew* - updated object or $ operators (e.g., $inc) which manipulate the object
*upsert* - if this should be an "upsert" operation; that is, if the record(s) do not exist, insert one. Upsert only inserts a single document.
*multi* - indicates if all documents matching criteria should be updated rather than just one. Can be useful with the $ operators below.

As such, in my case, I used the following to update my collection's field name:

<pre>Photo.collection.update({},
	{'$rename' => {'order_no_' => 'position'}},
	multi: true, safe: true)</pre>

Very simple, but very useful indeed!
