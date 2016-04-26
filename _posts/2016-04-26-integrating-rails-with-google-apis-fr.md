---
layout: post
title: "Pour intégrer Rails avec les API de Google"
description: "Utiliser un seul compte Google pour accéder leurs API pour créer des événements sur Google Calender de la part de vos utilisateurs, pour les envoyer des e-mails, etc, sans nécessiter leur autorisation."
modified: "Sat Apr 26 2016 12:30:00 GMT-0500 (Eastern Standard Time)"
comments: true
published: true
categories:
  - software development
tags:
  - ruby on rails
  - google api
  - oauth
  - google calendar
  - gmail
featured: false
---

Cet article va vous montrer comment employer un compte « maître » Google pour utiliser les services de Google de la part des utilisateurs de votre application sans nécessiter leur autorisation (Oauth). Par exemple, si vous aimeriez que votre application crée des événements sur Google Calendar pour votre organisation après que les utilisateurs demandent des rendez-vous, continuez à lire !

## Contexte

Je développe une application web qui est utilisée par les professeurs du département des Langues et Littératures Modernes à l’université de Grand Valley. Dans cette application, ils peuvent arranger des réservations dans notre laboratoire de langues, aussi bien que créer et gérer des projets de classe qui ont besoin des réservations pour des séances de formation et de révision.

Les réservations devraient être prévu sur notre Google Agenda afin que les autres professeurs, aussi bien que les étudiants, puissent voir quand chaque section de notre laboratoire sera ouverte sans rendez-vous, et quand elles sont fermées en raison des réservations. Ce faisant aide aussi les professeurs de trouver les bonnes dates et heures telles qu’il n’y a pas de conflit avec d’autres réservations.

L’itération précédente de cette application web utilisait [Google Apps Script](https://www.google.com/script/start), une solution dorsale basée sur JavaScript qui permet d’intégrer facilement avec les services de Google tels que Sheets, Agenda, et Gmail. Pourtant, une application Rails hébergée sur nos propres serveurs faut l’utilisation des [API de Google](https://console.developers.google.com/start) disponibles, surtout en utilisant [la bibliothèque client de Ruby](https://developers.google.com/api-client-library/ruby).

## Pour accéder à l’API avec un compte de service

Un [compte de service](https://developers.google.com/identity/protocols/OAuth2ServiceAccount) Google servira comme contrôleur de données, par exemple, les agendas, les e-mails, le stockage des données, etc., et agira de la part de vos utilisateurs (par exemple, en créant des événements sur Google Calendar pour eux, ou leur envoyant des e-mails), mais tout sans avoir besoin d’accès direct ni à leur compte Google, ni à leurs données. Vous pouvez simplement [créer un compte Google](https://accounts.google.com/signUp), et puis aller au [Google Developer Console](https://console.developers.google.com/project), et suivre les étapes comme décrits ci-dessous.

- **Créer un projet** – donnez-le n’importe quel nom que vous voulez (par exemple, *test-api*)

	![Create project]({{ site.url }}/images/integrating-rails-with-google-apis-fr/create-project.png)

-	**Utiliser les API de Google** – cliquez le lien pour *activer et gérer les API*.
	- Quelques API sont activées par défaut, mais activons Agenda et Mail

- Dans la section API Google Apps, **cliquez l’API Agenda et l’API Gmail pour les activer**
	- Vous serez invité à *Accéder à « Identifiants »* pour créer vos clés d’API. Finissez d’activer les deux API, puis cliquez *accéder à « Identifiants »*.

		![Download]({{ site.url }}/images/integrating-rails-with-google-apis-fr/go-to-credentials.png)

  - Sur cet écran vous verrez un lien vers [compte de service](https://console.developers.google.com/permissions/serviceaccounts). Allez-y et cliquez-le.

-	**Créer un compte de service** – cliquez le beau bouton bleu !
	- Donnez n’importe quel nom que vous voulez à votre compte de service (par exemple, *service-api*)
	- Cochez la case *Indiquer une nouvelle clé privée*, et faites le type de clé celui de JSON.
	- Cliquez *Créer*

	![Download]({{ site.url }}/images/integrating-rails-with-google-apis-fr/create-service-account.png)

Un fichier JSON vient d’être téléchargé sur votre ordinateur ; vous l’utilisez quand vous vous connectez aux divers clients d’API de Google. Ça devrait ressembler à quelque chose comme ça :


```
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
```

Nous allons maintenant tester l’API côté client de Ruby dans les étapes suivantes.

- Installez l’API côté client Ruby de Google avec `gem install google-api-client`, ou si vous préférez dans un Gemfile, `gem 'google-api-client', '~> 0.9.1'`.

Mettons en place un script bref pour énumérer les agendas auxquels notre compte de service a accès. La première chose que nous devrions faire est de créer une classe d’aide pour s’occuper de l’autorisation, de l’étendue et des variables de l’API. Cela permet un point d’accès à l’API Agenda plus centralisé.

```ruby
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
```

Créez un agenda et mettez à jour le `CALENDAR_ID` avec la valeur appropriée ; l’ID de l’agenda se trouve par *Paramètres de l’agenda*. Maintenant nous pouvons écrire un script pour tester l’API.

```ruby
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
```

Exécutant le script ci-dessus devrait sortir **No access to any calendar**. Le compte de service n’a pas accès aux ressources – il ne peut que faire des appels API. Par conséquent, toutes les ressources auxquelles vous voulez accéder avec l’API doivent être partagées avec l’adresse e-mail du compte de service. Sous *Paramètres de l’agenda*, partagez l’agenda avec votre compte de service, et puis exécutez à nouveau le script.

La sortie devrait maintenant afficher tous les agendas auxquels le compte de service a accès, par exemple, **CAL : Google API Test Calendar**. Maintenant nous pouvons créer un événement. Ajoutez le code suivant au script précédent :

```ruby
# Create a new hour-long event that takes place an hour from now
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
```

Vous devriez voir l’événement créé dans l’agenda dans une heure depuis que vous avez exécuté le script, qui dure une heure. Finalement, acquérons une liste des événements à venir :

```ruby
# List the next 10 upcoming events
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
```

D’autres API de Google, comme Mail, peuvent être aisément intégrés – tout est Ruby, donc ajouter ces fonctions dans une application Rails est une tâche simple. Voici des ressources que je trouvais utiles quand j’étais en train de tout faire marcher :

 - [Informations par défaut d'accès d'application](https://developers.google.com/identity/protocols/application-default-credentials) (alias informations d'accès de compte de service)
 - [Comptes de service](https://developers.google.com/api-client-library/ruby/auth/service-accounts)
 - [Référence de l'API Calendar](https://developers.google.com/google-apps/calendar/v3/reference)
 - [Rdoc de l'API Calendar](http://www.rubydoc.info/github/google/google-api-ruby-client/Google/Apis/CalendarV3)
 - [Explorateur de l'API Calendar](https://developers.google.com/apis-explorer/?hl=en_US#p/calendar/v3)
 - [Demandes d'API par lot](https://developers.google.com/google-apps/calendar/batch) - faites les demandes par lot pour raccourcir le temps de réponse
 - [Barres de progression en Rails](https://infinum.co/the-capsized-eight/articles/progress-bar-in-rails) - particulièrement utile si vous avez des processus longs d'API et vous ne voulez pas laisser les utilisateurs dans le noir au sujet du progrès