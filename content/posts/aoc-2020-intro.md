---
title: "Advent of Code 2020 - Intro"
summary: "It's time once again for my favorite annual holiday themed programming challenge!"
date: 2020-12-05T11:00:24-05:00
draft: false
tags:
  - Advent of Code
  - Tech
---

**For the past 5 years or so, a new holiday has joined the December lineup.** One celebrated on a smaller scale, perhaps, but which invites community and fellowship nonetheless. I'm talking, of course, of [Advent of Code](https://adventofcode.com/) (AoC). Developed by [Eric Wastl](https://twitter.com/ericwastl) in his "free time", AoC is an [advent calendar](https://en.wikipedia.org/wiki/Advent_calendar) for programmers, software engineers, and anyone looking to enhance their programming ability.

Each day from December 1 through December 25, a new puzzle is unlocked at midnight Eastern Time. Puzzles are presented in the form of a silly, whimsical story (usually about saving Christmas) and feature two parts. The first introduces the puzzle's concepts (e.g. passports, robots, snowflakes, chemical equations) and their representation in one's input, a text file. It then asks for a simple solution in the form of a string or number. Solving part one unlocks part two, which offers some twist or extension on part one. Depending on how one implemented the first part, part two might involve simply adding a log message or (in the worst possible case) a total refactor of one's solution.

The challenges generally increase in difficulty over time. The first few days tend to be trivial - parse input, loop through it, perform some operation. By mid-December, though, it's not unusual to encounter pathfinding algorithms, esoteric data structures, and other more advanced CS topics.

For each part successfully completed, "stars" are awarded. Twenty-five days, two parts each, fifty stars total for any given year. AoC has been running since 2015, so the true AoC grandmasters have worked through 260 challenges of varying intensity, as of today. According to a [recent tweet](https://twitter.com/ericwastl/status/1334614452822564864?s=20), that's only 400 programmers worldwide!

## So what's the point?

"Err, Mario," I hear you ask, "why bother? There are other programming challenge sites out there. Why not do one of those?"

Advent of Code is special to me for a few reasons. Unlike other challenge sites I've come across (e.g. HackerRank, Codewars, LeetCode), AoC stands out. It's small - only 260 puzzles built by one guy - and the community is active, passionate, and welcoming. The annual nature means interest in the challenge is concentrated into a few short days, and daily puzzles mean that most participants are working on the same one simultaneously. On the [AoC subreddit](https://www.reddit.com/r/adventofcode), solutions are posted alongside visualizations, pleas for assistance, and extra challenges by community members "upping the ante".

I've also been around to see it grow. According to my personal stats, I discovered AoC on 2015-12-2 and was fairly consistent in solving that year's puzzles.

At work, I founded the #adventofcode Slack channel and have been a vocal advocate from the beginning. Now 96 members strong, we have our own internal discussions every morning and are planning to hold a series of lightning talks in January to celebrate the event's conclusion. This gives me exposure to engineers I otherwise would have never encountered, and contributes to my personal brand of an excited, growth-oriented engineer.

From a more concrete perspective, AoC gives me an opportunity to practice approaching problems and learn new algorithms. I believe there's loads of value in being able to analyze a problem and decompose it into smaller, more manageable ones. I tend to approach most of the challenges from an object oriented perspective, so they're also good practice thinking about what those objects are, what they do, and how they interact.

While participating in a given year, certain concepts tend to recur - a certain visualization, a unique data structure, or 2019's infamous [Intcode computer](https://adventofcode.com/2019/day/2). When these concepts are reencountered, a sort of meta-challenge emerges in the creation of reusable libraries. For example, we encountered 2D grids several times in 2019, so I built up [Grid and Coordinate](https://github.com/mleone10/advent-of-code-2019/blob/master/grid.go) types that were reused several times. Here, the challenge was designing the correct abstraction and interface for that library - a skill that is invaluable when working on enterprise software.

In summary, I do Advent of Code because, quite frankly, it's fun! I like honing my skills, being involved in active communities, competing against friends, reading silly stories, and Christmas in general. AoC gives me all of that in one month-long dose!

## Interested?

If you're intrigued by the idea of Advent of Code and want to see what the fuss is about, I invite you to log in to [adventofcode.com](https://adventofcode.com/), click the first puzzle, and give it a shot! If you get stuck, ask for assistance in the [subreddit](https://www.reddit.com/r/adventofcode). And of course, [let me know](https://twitter.com/mleone5244) your thoughts!

I've come to look forward to Advent of Code as yet another fun holiday tradition. I hope, in time, you'll enjoy it just as much as I do!

Until next time,  
\- Mario
