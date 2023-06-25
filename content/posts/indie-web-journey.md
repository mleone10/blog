---
title: "The Start of an IndieWeb Journey"
summary: "I recently learned about the IndieWeb community, and I'm intrigued!"
date: 2023-06-20
draft: false
toc: false
tags:
  - IndieWeb
  - Blogging
---
New project!  I've been reading about ActivityPub, MicroPub, and the IndieWeb and it has me inspired.  My website at [mleone.dev](https://mleone.dev) is currently a static website hosted on GitHub and compiled from a stack of Markdown.  IndieWeb concepts, however, would allow me to publish articles and notes from any MicroPub-compatible client, send and receive replies and reactions via Webmentions, sign into other applications via IndieAuth, and syndicate content to other social media sites.  That sounds far more dynamic, and frankly way more fun.

I've found a few helpful resources so far.  [IndieWebify.me](https://indiewebify.me/) seems to lay out a "roadmap" of sorts for adopting IndieWeb concepts, which appeals to my project-management mindset.  Additionally, the [IndieWeb wiki](https://indieweb.org/) will come in handy.  I'll be referencing both throughout my journey.  Validator sites like [micropub.rocks](https://micropub.rocks/) and [webmention.rocks](https://webmention.rocks/) will help confirm everything is working as needed.

The architecture I'm currently envisioning is a static site that surfaces content from a headless CMS.  I'll use a third-party Micropub client to post content, and the whole thing will be hosted on AWS and configured via IaC.  Super clean, well-managed, cloud-native, and low-cost

So here's the order of operations as I see it:

1. Improve my static site to support core IndieWeb concepts:
    * Migrate my domain from Google Domains to AWS, since the former is being sold.
    * Set up Web Sign In (IndieAuth?) by configuring some static links on my existing static site.
    * Configure my static site to support h-card and h-entry on posts and my profile.
2. Support Micropub.  This will be a large effort that might entail building out a serverless `/micropub` API and custom CMS to back the blog.  Perhaps there's an opportunity to use HTMX to fetch content from a CloudFront-fronted headless CMS?
3. Support Webmentions.  I'm not sure what this'll entail yet, but I think I'll need another API to receive Webmentions, as well as some way to surface them on the site.  Then, I'll want a way to send them when I publish new posts/notes/replies via Micropub.
