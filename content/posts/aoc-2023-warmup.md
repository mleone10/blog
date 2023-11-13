---
title: "Warming Up for Advent of Code 2023"
summary: ""
date: 2023-11-12T23:00:02-05:00
toc: false
tags:
  - Advent of Code
  - Tech
  - Go
---
Advent of Code 2023 is almost here, and I'm managing my anticipation and excitement by starting on this year's repo, libraries, and scripts.  To practice, I decided to revisit previous years' puzzles and work through some of the ones I never solved.

## 2015 - Day 18
Day 18 of 2015 featured a grid of lights which toggled on and off in a simulation Conway's Game of Life.  I've never actually implemented the "game" before, so this felt like a personal milestone!

I recognized that I'd need a 2D grid of lights, which at first I representated as booleans.  I also knew that I'd need to implement a "step" mechanism to evaluate "turns" of the game.  In the interest of developing a library of tools to aid my 2023 journey, I implemented all of this as generic `Map`/`Filter`/`Reduce` functions on a [generic `Grid` type](https://github.com/mleone10/advent-of-code-2023/blob/master/internal/grid/grid.go).

The core of the `Grid` type is a struct that has two methods: `Get` and `Set`.  This type can be operated on by several functions:

* `Map`, which applies a function to each element in the Grid
* `Filter`, which removes elements of a Grid which do not adhere to a given condition
* `Reduce`, which iterates over elements of a Grid and aggregates a result
* `Length`, which counts the number of elements in a Grid

These common array functions unlock massive possibilities.  For example, to count the number of lights in the Grid which are "on", we use `Reduce`:
```golang
func (lg *LightGrid) NumOn() int {
  return grid.Reduce(lg.grid, 0, func(g grid.Grid[*Light], x, y int, v *Light, res int) int {
    if v.on {
      return res + 1
    }
    return res
  })
}
```

To implement game's "turns", we apply a function to each element with `Map`.  The core of that mapping is a function which is applied to each light once per step:

```golang
func lightIsOn(g grid.Grid[*Light], x, y int, v *Light) *Light {
  // In part two, some of the lights are stuck on.  This early return covers that case.
  if v, ok := g.Get(x, y); ok && v.stuck {
    return &Light{v.on, v.stuck}
  }

  // Iterate through all eight neighbors of the current light and count which ones are on.
  neighborsOn := 0
  for i := -1; i <= 1; i++ {
    for j := -1; j <= 1; j++ {
      if l, ok := g.Get(x+i, y+j); ok && l.on && !(i == 0 && j == 0) {
        neighborsOn++
      }
    }
  }

  return &Light{
    // This one line encapsulates the Game's rules: if a light is on and has two or three neighbors
    // that are also on, it stays on.  If it is off, it turns on if three neighbors are on.
    on:    (v.on && (neighborsOn == 2 || neighborsOn == 3)) || (!v.on && neighborsOn == 3),
    stuck: v.stuck,
  }
}
```

My favorite part of this solution is that, looking at [my solution code](https://github.com/mleone10/advent-of-code-2023/blob/master/days/2015day18/day18.go), most of the custom implementation is that `lightIsOn` method.  Most everything else, such as iteration over the grid and counting which lights are on, is handled by the generic Grid and the `Map`/`Filter`/`Reduce` functions.  That's super satisfying!

{{< signature >}}
