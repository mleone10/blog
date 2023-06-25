---
title: "Supporting Web Sign In, h-card, and h-entry"
summary: "Or, the most web design I've done in like a year."
date: 2023-06-25
draft: false
toc: false
tags:
  - IndieWeb
  - Blogging
---
With a few small changes, my site is now meeting some core IndieWeb criteria.  Specifically, it:

* Supports Web Sign In
* Contains a well-formatted h-card in the footer
* Marks up each post with h-entry classes

Web Sign In was easy - just add a link to my GitHub page in [the site's footer](https://github.com/mleone10/blog/commit/c5bf5489c52aa9a1fc3a939e7e867ea6bc95e748#diff-600598ff82d9d72e642b72196d69d7311e20d4ff372c88b2c94021afa844408eR37) with a `rel=me` attribute, and link back to my site on my GitHub page.  I've since added my Mastodon profile, too.

Adding an h-card presented more of a design challenge than an engineering one.  An h-card is just an HTML element with certain classes on its child elements.  My [first iteration](https://github.com/mleone10/blog/commit/77bf619921ac5a60c1cac47db8f410e0d2146fb5#diff-600598ff82d9d72e642b72196d69d7311e20d4ff372c88b2c94021afa844408eR36) of this featured my name, site URL, and an additional URL to my GitHub profile.  I added a `u-uid` attribute to my site link, tagging it as the page's "representational h-card".

However, two things were missing that I was encouraged by IndieWebify.me to add - a photo and a short bio.  I [found a way](https://github.com/mleone10/blog/commit/c7efe8cd0e8e5ed99e0540267002c8fd3d88d19a) to fit a small version of my profile picture and a short note in the footer, added the relevant class names, and my h-card was complete.  I'm pretty happy with how this new footer looks, which is a plus.

Finally, adding h-entry markup to each blog post was simple.  Mostly that just meant [adding class names](https://github.com/mleone10/blog/commit/1be753e7750d1432f264229ee451e4ebf353186c) to elements that were already there, though I did have to add a few URLs and `div`s for good measure.

The IndieWebify.me validators now show my site as conforming to all recommended aspects of these three facets.  Next come the hard parts - supporting Micropub and Webmentions.  I've seen a few ways to do this using a static site, but I think I'm going to take this opportunity to explore migrating the core of my site to some kind of dynamic system.  I have a few thoughts on how I might do that, so stay tuned for more!
