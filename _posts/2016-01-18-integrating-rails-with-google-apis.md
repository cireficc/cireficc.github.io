---
layout: post
title: "Integrating Rails With Google APIs"
description: "Using a single Google account to access APIs to create calendar events for your users, send them mail, etc, without getting their permission."
modified: "Sat Jan 16 2016 19:53:00 GMT-0500 (Eastern Standard Time)"
category: personal
comments: true
published: true
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

This article will show you how to use a "master" Google account to use Google services for users of your application without
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

## Getting API access with a service account

A Google [service account](https://developers.google.com/identity/protocols/OAuth2ServiceAccount) will serve as the controller of all data,
e.g. calendars, mail, drive storage, etc, and perform actions on behalf of your users (e.g. creating Calendar events for them, or sending them mail),
but without needing direct access to *their* Google account/data. You can simply [create a Google account](https://accounts.google.com/signUp),
and then immediately head over to the [Google developer console](https://console.developers.google.com/project), and follow the steps below.

- **Create a project** - give it any name that you want (e.g. *api-test*).

	![Create project]({{ site.url }}/images/integrating-rails-with-google-apis/create-project.png)

- **Use Google APIs** - click the link to *enable and manage APIs*.
	- Some APIs are enabled by default, but let's enable Calendar and Mail.

- Under the section Google Apps APIs, click and **enable Calendar and Mail**.
	- You will be prompted to *Go to credentials* in order to generate your API keys. Finish enabling both APIs, then click *go to credentials*.

		![Download]({{ site.url }}/images/integrating-rails-with-google-apis/go-to-credentials.png)

	- On this screen you will see a link to [service account](https://console.developers.google.com/permissions/serviceaccounts). Go ahead and click it.

- **Create service account** - click the pretty blue button!
	- Name your service account whatever you want (e.g. *api-service*)
	- Check the box for *Furnish a new private key, and make the key type JSON
	- Click *Create*

	![Download]({{ site.url }}/images/integrating-rails-with-google-apis/create-service-account.png)

A JSON file was just downloaded to your computer; you will use this when connecting to the various Google API clients. It should look something like this:

{% highlight json %}
{
  "type": "service_account",
  "project_id": "api-test-1244",
  "private_key_id": "676.....f7a",
  "private_key": "-----BEGIN PRIVATE KEY-----\nMII.....FwE=\n-----END PRIVATE KEY-----\n",
  "client_email": "api-service@api-test-1244.iam.gserviceaccount.com",
  "client_id": "108.....320",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://accounts.google.com/o/oauth2/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/api-service%40api-test-1244.iam.gserviceaccount.com"
}
{% endhighlight %}

We're going to test out the Ruby API client in the next few steps.

- Install the Ruby Google API client with `gem install google-api-client`, or if you prefer in a Gemfile, `gem 'google-api-client', '~> 0.9.1'`.

Let's get a quick script set up to list the calendars our service account has access to. The first thing we should do is create a helper class to handle authorization, API scoping and API variables. This allows for a more centralized access point to the Calendar API.

{% highlight ruby lineanchors %}
require 'googleauth'
require 'google/apis/calendar_v3'

class GoogleCalendarHelper
	
  attr_reader :calendar
  SCOPE = ["https://www.googleapis.com/auth/calendar"]
  CALENDAR_ID = 'tg7popn406fnihirr8119f7150@group.calendar.google.com'
	
  def initialize
	@calendar = Google::Apis::CalendarV3::CalendarService.new
	# get_application_default forces the use of this environment variable.
	# Set it to the location of your JSON credentials.
	ENV["GOOGLE_APPLICATION_CREDENTIALS"] = "api-test-key.json"
	@calendar.authorization = Google::Auth.get_application_default(SCOPE)
  end
end
{% endhighlight %}

Create a Google Calendar and update `CALENDAR_ID` to the appropriate value; the calendar's id can be found through *Calendar settings*. Now we can write a script to test the API.

{% highlight ruby lineanchors %}
require_relative 'google_calendar_helper'

# Initialize the API via our helper
@calendar_helper = GoogleCalendarHelper.new
@calendar = @calendar_helper.calendar

# List all calendars that the service account has access to
page_token = nil
begin
  result = @calendar.list_calendar_lists(page_token: page_token)
  if result.items.empty?
	puts "No access to any calendar!"
  else
	result.items.each do |c|
	  puts "CAL: #{c.summary}"
	end
	if result.next_page_token != page_token
	  page_token = result.next_page_token
	else
	  page_token = nil
	end
  end
end while !page_token.nil?
{% endhighlight %}

Running the above script should output **No access to any calendar!**. The service account does not own any resources - it can only make API calls. Therefore, any resources you want to access via the API must be shared with the service account's email address. Under *Calendar settings*, share the calendar with your service account, and re-run the script.

The output should now display any calendar that the service account can access, e.g. **CAL: Google API Test Calendar**. Now we can create an event. Add the following code to the previous script:

{% highlight ruby lineanchors %}
MINUTES_PER_DAY = 60 * 24
date_start = DateTime.now + Rational(60, MINUTES_PER_DAY)
date_end = date_start + Rational(60, MINUTES_PER_DAY)

event = Google::Apis::CalendarV3::Event.new({
  summary: 'Calendar API Test Event',
  location: 'Test',
  description: 'Testing the Calendar API',
  start: {
    date_time: date_start
  },
  end: {
    date_time: date_end
  },
  attendees: [
    {email: 'some_attendee@gmail.com'},
    {email: 'some_student@university.edu'}
  ]
})

result = @calendar.insert_event(GoogleCalendarHelper::CALENDAR_ID, event)
puts "Event created: #{result.to_yaml}"
{% endhighlight %}

You should see the event created in the calendar for an hour from when you ran the script, lasting an hour. Finally, let's get a list of upcoming events:

{% highlight ruby lineanchors %}
response = @calendar.list_events(GoogleCalendarHelper::CALENDAR_ID,
                              max_results: 10,
                              single_events: true,
                              order_by: 'startTime',
                              time_min: Time.now.iso8601)

puts "Upcoming events:"
puts "No upcoming events found" if response.items.empty?
response.items.each do |event|
  start = event.start.date || event.start.date_time
  puts "- #{event.summary} (#{start})"
  puts event.to_yaml
end
{% endhighlight %}

Other Google APIs, such as Mail, can be integrated easily - this is all Ruby, so dropping these functions into Rails is a simple task. Here are some resources that I found useful when getting this working:

 - [Application Default Credentials](https://developers.google.com/identity/protocols/application-default-credentials) (aka service account credentials)
 - [Service Accounts](https://developers.google.com/api-client-library/ruby/auth/service-accounts)
 - [Calendar API Reference](https://developers.google.com/google-apps/calendar/v3/reference)
 - [Calendar API Rdoc](http://www.rubydoc.info/github/google/google-api-ruby-client/Google/Apis/CalendarV3)
 - [Calendar API Explorer](https://developers.google.com/apis-explorer/?hl=en_US#p/calendar/v3)
 - [Progress bars in Rails](https://infinum.co/the-capsized-eight/articles/progress-bar-in-rails) - particularly useful if you'll have long-running API tasks and don't want to leave users in the dark about progress
 - [Batch API requests](https://developers.google.com/google-apps/calendar/batch) - do requests in batches to speed up response time

*[MLL]: Modern Languages and Literatures
*[GVSU]: Grand Valley State University
*[LRC]: Language Resource Center