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
{ log, getLines, max, sum, int } from ../utils.civet

lines := getLines import.meta.url
games := lines.map (l) => Array.from l.match /\d+ ./g

function isGamePossible(game: string[])
  game.every (draw) => int(draw) <= { r:12, g:13, b:14 }[draw.-1]

log 'Part 1', sum for game, id of games
  if isGamePossible game then id + 1

function maxPerColor(game: string[], color: string)
  game.filter(&.includes color).map(int) |> max

log 'Part 2', sum games.map (game) =>
  maxPerColor(game, 'r') *
  maxPerColor(game, 'g') *
  maxPerColor(game, 'b')
```
