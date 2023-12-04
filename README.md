Lang: [Civet](https://civet.dev)

# Advent of Code 2023

Something is wrong with global snow production, and you've been selected to take a look.
The Elves have even given you a map; on it, they've used stars to mark the top fifty
locations that are likely to be having problems.

## Day 1: Trebuchet?! ⭐⭐

```ts
{ getLines, log, sum, int } from ../utils.civet

lines := getLines import.meta.url

function parseValue(line: string)
  nr := line.replace /[a-z]/g, ''
  int nr.0 + nr.-1

function convertWords(line: string)
  line
    .replaceAll 'one', 'o1e'
    .replaceAll 'two', 't2o'
    .replaceAll 'three', 't3e'
    .replaceAll 'four', 'f4r'
    .replaceAll 'five', 'f5e'
    .replaceAll 'six', 's6x'
    .replaceAll 'seven', 's7n'
    .replaceAll 'eight', 'e8t'
    .replaceAll 'nine', 'n9e'
    .replaceAll 'zero', 'z0o'

log 'Part 1', sum lines.map(parseValue) 
log 'Part 2', sum lines.map(convertWords).map(parseValue)
```


## Day 2: Cube Conundrum ⭐⭐

```ts
{ log, getLines, max, sum, int, keys, multiply } from ../utils.civet

lines := getLines import.meta.url
games := lines.map (line) => Array.from line.match /\d+ ./g
limits := r:12, g:13, b:14

log 'Part 1', sum for game, id of games
  if game.every (draw) => int(draw) <= limits[draw.-1]
    id + 1

log 'Part 2', sum games.map (game) =>
  multiply keys(limits).map (color) =>
    game.filter(.includes color).map(int) |> max
```


## Day 3: Gear Ratios ⭐⭐

```ts
{ log, getLines, int, sum, flatten, values } from ../utils.civet

lines := getLines import.meta.url
parts: Record<string, number[]> := {}

for line, y of lines
  for m of line.matchAll /\d+/g
    for y of [y - 1..y + 1]
      for x of [m.index - 1..m.index + m.0.length]
        if char := lines[y]?.[x]
          unless /[0-9.]/.test char
            (parts.`${x},${y}` ??= []).push int m.0

log 'Part 1', sum flatten values parts
log 'Part 2', sum values(parts).filter(.length > 1).map (n) => n.0 * n.1
```
