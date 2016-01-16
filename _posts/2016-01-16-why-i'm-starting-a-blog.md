---
layout: post
title: "Why I'm Starting a Blog"
description: "Rationale as to why I'm starting a blog"
modified: "Fri Jan 15 2016 19:00:00 GMT-0500 (Eastern Standard Time)"
category: personal
comments: true
published: true
categories: 
  - personal
featured: true
---



In my last 5 years of higher education, I haven't done very much to document my work, how I solved problems, or just
have a place to talk about things that I care about. I have also come to realize that I have very little web presence
aside from my Linkedin profile and some activity on sites such as [Stack Exchange](http://stackexchange.com/users/2255622/chris-cirefice).
In order to solve those problems, I decided to start a blog.

Doing so will allow me to have a personal archive of things that I have accomplished, and how I accomplished them.
As well, my blog posts on programming may be of use to other developers in the future. My blog posts on languages
such as Japanese may be of use to those learning the language and having difficulty with the grammar. The goal is to
reinforce my own knowledge of these topics as well as provide *information* and *guidance* to others, something
that I have been greatly appreciative of the last few years as I learn more and more.

Lastly, future employers may stumble across my blog and get a better idea of how I overcome struggles in programming,
and have a better understanding of how I communicate. That would of course be beneficial to me, but it's not the *main*
goal.

After I decided to start a blog, I had a bit of trouble figuring out what tools to use to make one. I had a few requirements
that I [documented a long time ago](http://softwarerecs.stackexchange.com/questions/7519/multilingual-blogging-platform):

1. **Lightweight platform** - *Wordpress* was simply not an option
2. **Integration with SCM** (e.g. Github, Bitbucket) - if I didn't have revision history, it was a no-go
3. **Code formatting** - this was an absolute **must**, as at least half the things I planned on blogging about were code-related
4. **Low-cost or free hosting** - I didn't want to have to run my own server for the blog, and I didn't want to pay a lot for it either
5. **Full content control** - I wanted to be able to take the platform/framework and mold it to my own needs
6. **Multilingual content capability** - I needed to be able to translate my posts into different languages for greater accessibility
7. **Custom domain name** - while not an immediate *must-have*, being able to have a custom domain is good for SEO and publicity

There were more requirements, but those are the main ones. After a long time spent searching, I finally found the perfect combination:
[Jekyll](https://jekyllrb.com) + [Github Pages](https://pages.github.com)! This combination allows you to write posts in
[Markdown](https://en.wikipedia.org/wiki/Markdown) and a pseudo-Ruby templating language called [Liquid](http://liquidmarkup.org),
generating beautiful HTML pages without all the crappy HTML (because who wants to write that?). And of course I'm not a web developer
and have very little creativity when it comes to design, so I also needed a theme for the blog. After some searching, I found an
awesome theme called [Notepad](https://github.com/hmfaysal/Notepad) created by the ever-awesome [Hossain M. Faysal](https://twitter.com/hmfaysal).

I got the blog all set up on [Cloud9](https://c9.io) (a web IDE) and started writing this post! I'll get to customizing things a bit more
later when I have the time (*said every programmer ever*) but for now, this solution is a really good one.

*[SCM]: source code management
*[SEO]: search engine optimization
*[IDE]: Interactive Development Environment