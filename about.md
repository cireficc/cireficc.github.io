---
layout: page
permalink: /about/index.html
title: Christopher S. Cirefice
tags: [Cirefice, S., Christopher, cireficc, christophercirefice]
chart: true
---
<figure>
  <img src="{{ site.url }}/images/chris-cirefice.jpg" alt="Christopher S. Cirefice">
  <figcaption>Christopher S. Cirefice</figcaption>
</figure>

{% assign featuredcount = 0 %}
{% assign statuscount = 0 %}

{% for post in site.posts %}
    {% if post.featured %}
    	{% assign featuredcount = featuredcount | plus: 1 %}
    {% endif %}
{% endfor %}


My name is **Christopher S. Cirefice**, and this is my personal blog.

I am a Computer Science and French double-major with a minor in Applied Linguistics at Grand Valley State University.
I am also studying Japanese and plan to start learning Russian in the near future. Other interests of mine include
writing/playing music (singing, piano, guitar, ukelele, mandolin) as well as playing tennis and cooking.

I plan to pursue a MS and PhD in computational linguistics, specifically in the area of generative grammars and syntax analysis,
developing the capability of computers to interpret natural languages.

I currently work two part-time jobs: the first is at the [Language Resource Center](http://marvin.mll.gvsu.edu/lrc) (GVSU),
the other for a language application development company called [VidaLingua](http://vidalingua.com) where I develop a social
phrase translation application [PhraseMates](http://www.phrasemates.com), as well as maintain a set of
[Android bilingual dictionaries](http://www.vidalingua.com/android-dictionary). I use Ruby on Rails, Node.js, PostgreSQL and
Android in my work.

*[MLL]: Modern Languages and Literatures

---

My blog currently has {{ site.posts | size }} posts in <a href="{{ site.url }}/categories">{{ site.categories | size }} categories</a>.
{% if featuredcount != 0 %}There are <a href="{{ site.url }}/featured">{{ featuredcount }} featured posts</a>; I encourage you to read them!{% endif %}
The most recent post is {% for post in site.posts limit:1 %}{% if post.description %}<a href="{{ site.url }}{{ post.url }}" title="{{ post.description }}">"{{ post.title }}"</a>{% else %}<a href="{{ site.url }}{{ post.url }}" title="{{ post.description }}" title="Read more about {{ post.title }}">"{{ post.title }}"</a>{% endif %}{% endfor %}
which was published on {% for post in site.posts limit:1 %}{% assign modifiedtime = post.modified | date: "%Y%m%d" %}{% assign posttime = post.date | date: "%Y%m%d" %}<time datetime="{{ post.date | date_to_xmlschema }}" class="post-time">{{ post.date | date: "%d %b %Y" }}</time>{% if post.modified %}{% if modifiedtime != posttime %} and last modified on<time datetime="{{ post.modified | date: "%Y-%m-%d" }}" itemprop="dateModified">{{ post.modified | date: "%d %b %Y" }}</time>{% endif %}{% endif %}
{% endfor %}.
The last update was on {{ site.time | date: "%A, %d %b %Y" }} at {{ site.time | date: "%I:%M %p" }} UTC.