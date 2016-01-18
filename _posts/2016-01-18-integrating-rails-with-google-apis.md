---
layout: post
title: "Integrating Rails With Google APIs"
description: "Using a single Google account to access APIs to create calendar events for your users, send them mail, etc, without getting their permission."
modified: "Sat Jan 16 2016 19:53:00 GMT-0500 (Eastern Standard Time)"
category: personal
comments: true
published: false
categories: 
  - computerscience
tags:
  - ruby on rails
  - google api
  - oauth
  - google calendar
  - gmail
featured: false
---

This article will show you how to use a "master" Google account to host and maintain data on Google services for users of your application without
needing to get their (Oauth) consent. If you need, for example, to have your web application create Google Calendar events for your organization
after users request dates/times, continue reading!

## Background

I develop a web application used by the faculty in the MLL department at GVSU. In this application, faculty can schedule lab reservations in the LRC,
as well as create and manage class projects which require reservations for training and editing sessions.

Reservations should be scheduled on our Google Calendar so that other faculty, as well as students, can see when our lab sections will be open for
walk-ins, and when they are closed for reservations. Doing so also aids faculty to find dates and times where there are no conflicts with other
reservations.

The previous iteration of this web application used [Google Apps Script](https://www.google.com/script/start), a JavaScript-based back-end solution
that allowed easy integration with Google services such as Spreadsheets, Calendar, and Gmail. However, a Rails application hosted on our own servers
requires usage of the available [Google APIs](https://console.developers.google.com/start), specifically through using the
[Ruby Client Library](https://developers.google.com/api-client-library/ruby).

## A bit of research

When I initially started, I wasted a lot of time looking into the complexities of [*service accounts*](https://developers.google.com/identity/protocols/OAuth2ServiceAccount),
because one would initially think "*oh, I have a web app (server application) communicating with Google, so I should use a service account*".
**Wrong**. Well, not *completely* wrong. Service accounts are designed to allow a web application to call Google APIs without necessarily interacting
with Google user data. However, setting up a service account is very complex and there's essentially no documentation or example code on *how to use it*!
The easier method is to simply use a *personal* account that is "in charge" of all the data, and use *that* to access the APIs. Our lab has a Google
account that hosts our calendars, email, Drive storage, etc., so it made sense to completely bypass the service account scenario and simply authorize
**our** Google account to use the APIs.

## Getting API access with a "boss" account

When I say *boss account* I mean a Google account that will serve as the owner of all data, e.g. calendars, mail, drive storage, etc, and perform
actions on behalf of your users (e.g. creating Calendar events for them, or sending them mail), but without needing direct access to *their* Google
account/data. You can simply [create a Google account](https://accounts.google.com/signUp), and then immediately head over to the [Google developer
console](https://console.developers.google.com/project), and follow the steps below.

1. **Create a project** - give it any name that you want (e.g. api-test)
 - wait for the project creation to finish
2. **Use Google APIs** - click the link to *enable and manage APIs*
 - Some APIs are enabled by default, but let's enable Calendar and Mail
3. Under the section Google Apps APIs, click and **enable Calendar and Mail**
 - You will be prompted to *Go to credentials* in order to generate your API keys. Finish enabling both APIs, then click that dialog
4. **Get credentials** - choose *New credentials*, and select *Oauth client ID*
 - An *Oauth client ID* is **normally** used on a per-user authorization basis when you request to access their data, e.g. requesting to access
a user's Google Calendar, or their Facebook profile. However, we're going to manually authorize our "boss" account and use *it* and it only to
access Google APIs.
 - Configure the *consent screen*; this screen will only be shown once to one person (your "boss" account), so it is not necessary to fill it
out entirely. Just enter the *product name* and save.
5. For the *application type*, choose ***other*** and give it a name
 - At this point you will be shown the **client_id** and **client_secret** - these are the important things, **do not share this information**!
6. Close the dialog and click the download button to download the JSON file containing the API credentials: ![Download]({{ site.url }}/images/integrating-rails-with-google-apis/download.png)
7. Install the Ruby Google API client with `gem install google-api-client`, or if you prefer in a Gemfile:
{% highlight ruby linenos=table %}
source 'https://rubygems.org'

gem 'google-api-client', '~> 0.9'

def func
    puts "Hello"
end

def func
    puts "Hello"
end

def func
    puts "Hello"
end

def func
    puts "Hello"
end

def func
    puts "Hello"
end

def func
    puts "Hello"
end
{% endhighlight %}

*[MLL]: Modern Languages and Literatures
*[GVSU]: Grand Valley State University
*[LRC]: Language Resource Center