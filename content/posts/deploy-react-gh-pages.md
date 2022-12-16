---
title: "Deploying a React App to GitHub Pages"
summary: "A simple workflow opens up a world of possibilities."
date: 2020-08-07T22:33:41-04:00
draft: false
tags:
  - Tech
---

**Around the same time that quarantine started in earnest in March of this year, I was lucky enough to be invited to join a new Dungeons and Dragons campaign.**

The Dungeon Master is one I've run games with before, and his adventures are always intricate webs of mystery featuring a wide array of characters. Of course, I jumped at the chance. I worked with another player to create twin Pallid Elves named Letheryl and Llyrethe and have been having a blast exploring [Wildemount](https://dnd.wizards.com/products/wildemount) every week since.

At first, I managed Letheryl on a simple Google Doc. That gave me great portability and ease of editing, and honestly was perfectly sufficient. But soon after we started, I got that familiar urge to build something. Thus the first iteration of my character sheet webpage was born.

The first page (hosted at [dnd.marioleone.me](https://dnd.marioleone.me)) was a single `index.html` file, plus an `index.css` file to make it prettier. I used GitHub's excellent hosting capability, [GitHub Pages](https://pages.github.com/), to expose the website to the world, and configured a Route53 rule on AWS (my domain host of choice) to direct the domain to it.

Before long though, I wanted to grow the page beyond what simple HTML would offer. At the same time, I wanted to expand my own frontend knowledge - my technical experience is almost entirely backend - and [React](https://reactjs.org/) caught my eye. I considered Angular and Vue, but I liked React's focus on minimalism. In time, I got most of the quantitative parts of Letheryl's character sheet converted, and was ready to get the new version launched.

As a static page, there was no reason I couldn't continue to use GitHub Pages. However, whereas the original page was HTML that GitHub could automatically parse and deploy, the new React-based version required a build step before it could be similarly interpretted. Enter [GitHub Actions](https://github.com/features/actions).

GitHub Actions are GitHub's CI/CD automation solution. Like Jenkins or TravisCI (with which I have plenty of experience), GitHub Actions allow users to execute scripts on common triggers, like pushing to a GitHub branch or opening a pull request.

I already knew that my webpage could be built locally with the right environment configured:

```bash
$ npm install
$ npm run build
```

I also knew that installing Node and executing NPM commands within a GitHub Actions was a common task:

```yaml
name: Build React App

on:
  push:
    branches: [master]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Install Node
        uses: actions/setup-node@v1
        with:
          node-version: 12
      - name: Install Node packages
        run: npm install
      - name: Build page
        run: npm run build
```

The only unknown then was getting the built artifact (a `build` directory containing browser-compatible HTML) pushed into my repository. I chose to push it to a separate `gh-pages` branch for two reasons:

- I didn't want the build artifact in `master` branch, since it would unnecessarily add to the repo's size.
- GitHub Actions won't trigger a GitHub Pages build when the action pushes to the branch which triggered the original Action. That is, GitHub (wisely) protects against recursive Action executions.

So the only thing left was to create a `deploy` step in my workflow, as such:

```yaml
- name: Deploy page
        run: |
          mv build docs
          cp CNAME docs
          git config --local user.email "${{ secrets.EMAIL }}"
          git config --local user.name "mleone10"
          git add -A
          git commit -m "Updating site"
          git push -f "https://${{ env.GITHUB_ACTOR }}:${{ secrets.GITHUB_TOKEN }}@github.com/mleone10/dnd-site.git" HEAD:gh-pages
```

Some explaination is in order:

- GitHub Pages can either read HTML from a branch's root or a `docs` directory, so we first rename React's `build` to `docs`.
- Custom domains are stored using a special `CNAME` file containing the domain. This file must exist in the host directory - `docs` in my case.
- Git needs to know who is pushing to the repository, so we configure the Action-specific container with my GitHub username and email. In order to keep my email address hidden, I [stored it as a repository secret](https://docs.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets#creating-encrypted-secrets-for-a-repository).
- `git add -A` stages the untracked `docs` directory, and `git commit ...` commits it.
- Finally, the workflow forcibly pushes the artifact to the `gh-pages` branch. Authentication is done using two built-in environment variables to specify the user who triggered the GitHub Action (me &#128512;) and an Action-specific token which provides easy authentication.

All together then, the following was placed within my repo's `master` branch at `.github/workflows/build.yaml`:

```yaml
name: Deploy React App

on:
  push:
    branches: [master]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Install Node
        uses: actions/setup-node@v1
        with:
          node-version: 12
      - name: Install Node packages
        run: npm install
      - name: Build page
        run: npm run build
      - name: Deploy page
        run: |
          mv build docs
          cp CNAME docs
          git config --local user.email "${{ secrets.EMAIL }}"
          git config --local user.name "mleone10"
          git add -A
          git commit -m "Updating site"
          git push -f "https://${{ env.GITHUB_ACTOR }}:${{ secrets.GITHUB_TOKEN }}@github.com/mleone10/dnd-site.git" HEAD:gh-pages
```

And there you have it! Automatic React builds on pushes to the `master` branch with zero third-party dependencies. That's a total success in my book.

I hope this was a helpful guide through the process of automatically deploying a React app to GitHub Pages. Find the project's full source code [here](https://github.com/mleone10/dnd-site).

Until next time,  
\- Mario
