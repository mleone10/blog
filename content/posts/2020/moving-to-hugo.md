---
title: "Moving to Hugo"
summary: "My experiences migrating from a simple HTML website to a Hugo-built one"
date: 2020-07-22T19:42:58-04:00
draft: true
tags:
    - Blogging
---

Since I first bought marioleone.me, the domain has really only hosted a plain HTML file.  Coupled with some CSS, it was a fun foray into layouts and styling.  I always wanted to grow it into something more, though.

At first I tried experimenting with [Vue.js](https://vuejs.org/) on the recommendation of a friend.  I liked the separation of concerns within a single file, but was quickly overwhelmed as I had absolutely zero JavaScript experience.  So the humble HTML file stayed.

Fast forward a bit and I started experimenting with [React](https://reactjs.org/).  The appeal of a lightweight library to add bits of functionality appealed to me, but a fullfledged blogging platform was clearly beyond my reach.  And, quite frankly, beyond my interest.

So finally I arrived at static site generators like [Jekyll](https://jekyllrb.com/) and [Hugo](https://gohugo.io/).  Jekyll certainly has a larger community, but it's written in Ruby, which I'm only *barely* familiar with.  Hugo, meanwhile, is written in Go, which I'm much more comfortable with, and supports faster builds than Jekyll.

The Hugo installation instructions are simple, but there was a slight learning curve to understand how content, partials, and themes worked together.  After an evening of fiddling, I managed to get a suitable site up and running locally.