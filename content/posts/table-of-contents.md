---
title: "Using Hugo's Built-In Table of Contents"
summary: "Leveraging a standard Hugo feature for more navigable content."
date: 2022-12-24T22:44:45-05:00
toc: true
tags:
  - Blogging
  - Software
  - Hugo
---

I wanted to find a way to add a table of contents to certain blog posts.  Luckily, Hugo provides this feature out of the box.  All it takes is some simple config!

## Expected Result
Before we get into how I'm generating the table of contents above, let's first look at what we can hope to achieve.  Per [the documentation](https://gohugo.io/content-management/toc/), Hugo will automatically generate a table of contents from a page's Markdown headings.  This particular post contains several `h2` and `h3` elements which are parsed into the outline beneath the this post's title.

## Customization
Hugo's table of contents is an unordered, bulleted list by default.  We can change this to an ordered list if we prefer by setting `markup.tableOfContents.ordered` to `true` in our site parameters.

Hugo will include `h2` and `h3` elements by default.  We can modify this by changing two properties:

* `markup.tableOfContents.startLevel` (default `2`) to set the highest level of heading rendered
* `markup.tableOfContents.endLevel` (default `3`) to set the lowest

In the rendered page, the table is a `nav` block with `id="TableOfContents` containing a series of nested `ul` blocks.  We can use this to our advantage to style the output as we see fit!

## Integration

So how do we go about adding this to our blog posts?  Individual posts are most likely rendered using the template file at `layouts/_defaults/single.html`.  Mine looks like this:

```html {linenos=true, hl_lines=[9]}
{{ define "main" }}
<article class="single">
  <header>
    <h2>{{ .Title }}</h2>
    <span class="byline">
      <!--  Author name, date, and relevant tag details omitted -->
    </span>
  </header>
  {{ block "tableOfContents" . }}{{ end }}
  {{ .Content }}
</article>
{{ end }}
```

The highlighted line references a Hugo partial I created to encompass the entire Table of Contents block.  I put that in `layouts/partials/tableOfContents.html`:

```html {linenos=true}
{{ define "tableOfContents" }}
{{ if .Params.toc }}
<aside class="tableOfContents boxed">
  <h3>Contents</h3>
  {{ .TableOfContents }}
</aside>
{{ end }}
{{ end }}
```

The first and last lines define the partial as something other templates can import.  Line 2 gives us conditional rendering - if for some reason we don't want a table of contents, we should be able to disable it.  This conditional checks for a `toc: true` line in the post's front-matter.  Only when it succeeds will the table be rendered.

For the actual HTML, we just wrap an `h3` header and Hugo's `.TableOfContents` variables in an `aside` block.  Then I use a couple classes to tweak the margins.

One minor pitfall I encountered in styling the table was due to it being a `nav` element.  My site's main navigation bar at the top of the page is also a `nav`, and my original CSS was overly broad.  So the main nav-bar's styles were unintentionally applying to the table.  I solved that by adding a `mainNav` class to the main nav-bar and changing its CSS to only apply to `nav`s with that class.

So there you have it!  Painless, automatic tables of contents on future posts if I choose to enable them.  As always, this blog's source is available on GitHub.  The table of contents partial is [here](https://github.com/mleone10/blog/blob/master/themes/ditalini/layouts/partials/tableOfContents.html), and it's CSS is [here](https://github.com/mleone10/blog/blob/master/themes/ditalini/static/css/single.css#L37).

{{< signature >}}
