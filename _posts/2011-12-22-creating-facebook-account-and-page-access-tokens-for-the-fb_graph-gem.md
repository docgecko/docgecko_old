---
layout: article
title: "Creating Facebook Account and Page access tokens for the fb_graph gem"
categories: articles
image:
  teaser: facebook-developer.jpg
---

I have a jobs board app built in Ruby on Rails ([http://infrajobshq.com](http://infrajobshq.com)).  As jobs are posted to the site by job advertisers, the app posts shortened information about each jobs to both Twitter and Facebook pages.

Recently, the app started failing when jobs were posted and I wasn't sure why.  After investigation, I found that it was because I had changed by Facebook password.  I didn't realise that the password is also associated with the ACCESS_TOKEN of Facebook Graph API.

So, again I needed to go through the process to generate the ACCESS_TOKEN for my ap As such, I thought I would post the process as it requires a little bit of work and is not necessarily an easy process to remember: I had to go through a number of pages on the Facebook Developers section in the Facebook Graph API ([https://developers.facebook.com/docs/reference/api/](https://developers.facebook.com/docs/reference/api/)).

The process is a **2 step process**. Firstly you have to obtain an ACCESS_TOKEN that gains you access to your Facebook account.  Once you have that level of access, you then need to find the access token of your Facebook Page you would like to gain access to.  So here goes:

**1. Step 1: Obtain Facebook Account Access Token**

In order to gain your Facebook account ACCESS TOKEN, you can use the following url (in your chosen browser):

<pre>https://www.facebook.com/dialog/oauth?
     client_id=YOUR_APP_ID&redirect_uri=YOUR_URL&scope=manage_pages&
     response_type=token
</pre>

YOUR_APP_ID and YOUR_URL can be found on your personal Facebook Developers page, where you apps exist, i.e.:

<pre>https://developers.facebook.com/apps</pre>

As you can see from this page, under the Summary section, you can find your:

* App ID/API Key
* Site URL

So, substituting YOUR_APP_ID with your "App ID/API Key" and YOUR_URL with your "Site URL", you will have a url that looks something like this:

<pre>https://www.facebook.com/dialog/oauth?
     client_id=542148432111423&redirect_uri=http://infrajobshq.com/&scope=manage_pages&
     response_type=token
</pre>

As you enter this url, you will be redirected to your site's url, in my case to http://infrajobshq.com.  And now, as you will see from the url, it contains additional information after sites main address, e.g. it looks something like this:

<pre>http://infrajobshq.com/#access_token=BBBDGIb5OYp4BAJK0qYw8qw3fY4uPps23xfMpvobHvVCN4OsI50LGIshL8fUZA
		 xQsgnUt45b6kAgtFNm3X4wezyvK7VWEZD&expires_in=0</pre>

As you can see, the url contains the ACCESS TOKEN for your Facebook Account.  You will need this for the next step.


**2. Step 2: Obtain Facebook Page Access Token**

Now, to find the ACCESS TOKEN for your Facebook Page, use the following url:

<pre>https://graph.facebook.com/me/accounts?access_token=ACCESS_TOKEN</pre>

Simply replace the ACCESS_TOKEN with your Facebook Account ACCESS_TOKEN (and no need to include the bit on the end of the url that is '&expires_in=0').  In my case, the url looks like this:

<pre>https://graph.facebook.com/me/accounts?access_token=BBBDGIb5OYp4BAJK0qYw8qw3fY4uPps23xfMpvobHvVCN4O
		 sI50LGIshL8fUZAxQsgnUt45b6kAgtFNm3X4wezyvK7VWEZD</pre>

Once you have entered this, you will be given a page that contains your Facebook Page ACCESS_TOKEN, e.g.:

<pre>{
   "data": [
      {
         "name": "InfraJobs",
         "access_token": "BBBDGIb8OUp4BDNWFlERsVcEaeS1lylDAqpN3FtDET4L2fdeS6TPoNvYZAZBbzeDXC6TKTB8w
  LnX3wPZA0HU8n6pvZCmRndGwOcQmvU1T4BAZDZD",
         "category": "Consulting/business services",
         "id": "130231394756745"
      },
      {
         "name": "InfraJobs",
         "access_token": "AAADGIb8OYp4BAMJBpufj6LhsDq2W8JhCpQW6P1zjZCK32uwuK0njrRItJybbfFPXKEOsZCCf
  4Sfjxkmk4dcJz43OQN8ttOKoPZClOnJZCwZDZD",
         "category": "Application",
         "id": "327845241634422"
      }
   ],
   "paging": {
      "next": "https://graph.facebook.com/me/accounts?access_token=BBBDGIb5OYp4BAJK0qYw8qw3fY4uPps2
  3xfMpvobHvVCN4OsI50LGIshL8fUZAxQsgnUt45b6kAgtFNm3X4wezyvK7VWEZD&limit=5000&offset=5000&_
  _after_id=327845241634422"
   }
}</pre>

As you see, there are two sets of data related to my site.  Once relates to my Facebook Page and the other relates to the Facebook App of the same name.  The ACCESS_TOKEN you require is the one related to the Facebook Page: in my case its the the first once in the list.

Wow, so many ACCESS_TOKENs hey!

Anyway, I am using these codes in my App to post to my Facebook Page, so my Ruby on Rails code look like this:

<pre>def facebook(message)
  fb_message = message(message) + ": " + job.short_url
  me = FbGraph::User.me("BBBDGIb5OYp4BAJK0qYw8qw3fY4uPps23xfMpvobHvVCN4OsI50LGIshL8fUZAxQsgnUt45b6k
	AgtFNm3X4wezyvK7VWEZD")
  me.feed!(
    :access_token => "BBBDGIb8OUp4BDNWFlERsVcEaeS1lylDAqpN3FtDET4L2fdeS6TPoNvYZAZBbzeDXC6TKTB8wLnX3
		wPZA0HU8n6pvZCmRndGwOcQmvU1T4BAZDZD",
    :message => fb_message
  )
end</pre>

As you can see, the Facebook Account ACCESS_TOKEN is used in the 3rd line of code, and the Facebook Page ACCESS_TOKEN is used in the 5th line of code.  The first one gains me access to my account and the second that allows access to the the Page's feed.

Damn, finally got there.  I realise now that I was a little confused the first time I did this, simply as there were a few ACCESS_TOKENs knocking about, and that I wasn't initially clear from the Facebook Developer Graph API Authentication which urls I should have been using.  But now after going through the process, its quite straight-forward.  Even so, glad its all written down here now :-).
