Lang: [Civet](https://civet.dev)

# Advent of Code 2023

Something is wrong with global snow production, and you've been selected to take a look.
The Elves have even given you a map; on it, they've used stars to mark the top fifty
locations that are likely to be having problems.

## Day 13: Point of Incidence ⭐⭐

```ts
{ log, getInput, sum, zip } from ../utils.civet

patterns := getInput import.meta.url
  .split('\n\n').map &.split('\n')

function diffs(str1: string, str2: string)
  sum for c, i of str1
    c is not str2[i]

function rotate(pattern: string[])
  zip(...pattern.map &.split('')).flatMap &.join('')

function searchMirror(pattern: string[], possibleErrors: number)
  for y of [0...pattern.length - 1]
    errors .= 0
    for i .= 0; y - i >= 0 and y + i + 1 < pattern.length; i++
      errors += diffs pattern[y - i], pattern[y + i + 1]
    return y + 1 if errors is possibleErrors
  0

function getNote(pattern: string[], possibleErrors: number)
  100 * searchMirror(pattern, possibleErrors) +
  searchMirror(rotate(pattern), possibleErrors)

log 'Part 1', sum patterns.map (p) => getNote p, 0
log 'Part 2', sum patterns.map (p) => getNote p, 1
```


## Day 12: Hot Springs ⭐⭐

```ts
{ log, getLines, toNumbers, sum } from ../utils.civet

lines := getLines(import.meta.url).map &.split ' '
mem := {}

function combinations(springs: string, groups: number[], counter = 0): number
  memKey := springs + groups
  return mem[memKey] if mem[memKey]
  return '#' is in springs ? 0 : 1 unless groups.length
  [nr, ...rest] := groups
  for i .= 0; i <= springs.length - rest.length - sum(rest) - nr; i++
    break if '#' is in springs[0...i]
    if '.' is not in springs[i...i + nr] and springs[i + nr] is not "#"
      counter += combinations springs[i + nr + 1..], rest
  mem[memKey] = counter

log 'Part 1', sum for [springs, groups] of lines
  combinations springs, toNumbers groups

log 'Part 2', sum for [springs, groups] of lines
  combinations 
    new Array(5).fill(springs).join('?'),
    new Array(5).fill(toNumbers groups).flatMap (&)
```


## Day 11: Cosmic Expansion ⭐⭐

```ts
{ log, getLines, sum } from ../utils.civet

lines .= getLines import.meta.url

expandingRows := []
for line, y of lines
  expandingRows.push y unless '#' is in line

expandingCols := []
for x of [0...lines.0.length]
  expandingCols.push x if lines.every &[x] is '.'

galaxies := []
for y of [0...lines.length]
  for x of [0...lines.0.length]
    galaxies.push [x, y] if lines[y][x] is '#'

function getDistance([[x1, y1], [x2, y2]])
  Math.abs(x1 - x2) + Math.abs(y1 - y2)

function getPairs(galaxies: number[][])
  for i of [0...galaxies.length]
    for j of [i + 1...galaxies.length]
      [galaxies[i], galaxies[j]]

function expand(galaxies: number[][], multiplayer: number)
  for [x, y] of galaxies
    r := expandingRows.filter(& < y).length
    c := expandingCols.filter(& < x).length
    [x + c * (multiplayer - 1), y + r * (multiplayer - 1)]

function sumDistances(galaxies: number[][], expandMultiplier: number)
  galaxies = expand galaxies, expandMultiplier
  sum getPairs(galaxies).flatMap (&).map getDistance

log 'Part 1', sumDistances galaxies, 2
log 'Part 2', sumDistances galaxies, 1e6
```


## Day 10: Pipe Maze ⭐⭐

```ts
pointInPolygon from 'point-in-polygon'
{ log, getLines } from ../utils.civet

lines := getLines import.meta.url
startY := lines.findIndex &.includes 'S'
startX := lines[startY].indexOf 'S'

last .= [startX, startY]
current .= [startX, startY + 1]
polygon := [current]

while next := move current, last
  last = current
  polygon.push current = next

function move([x, y]: number[], [prevX, prevY]: number[])
  disabledDir .= switch prevX - x
    > 0 then 'E'
    < 0 then 'W'
    else prevY - y < 0 ? 'N' : 'S'

  switch lines[y][x] + disabledDir
    '-E' [x - 1, y]
    '-W' [x + 1, y]
    '|N' [x, y + 1]
    '|S' [x, y - 1]
    '7W' [x, y + 1]
    '7S' [x - 1, y]
    'LN' [x + 1, y]
    'LE' [x, y - 1]
    'JW' [x, y - 1]
    'JN' [x - 1, y]
    'FS' [x + 1, y]
    'FE' [x, y + 1]

log 'Part 1', polygon.length / 2

area .= 0
for y of [0...lines.length]
  for x of [0...lines.length]
    unless polygon.some [px, py] => px is x and py is y
      area++ if pointInPolygon [x, y], polygon

log 'Part 2', area
```


## Day 9: Mirage Maintenance ⭐⭐

```ts
{ log, getLines, toNumbers, sum } from ../utils.civet

lines := getLines(import.meta.url).map toNumbers

function getSequence(arr: number[])
  for i of [1...arr.length]
    arr[i] - arr[i - 1]

function predict(arr: number[])
  arrs := [arr]
  while sum arrs.-1
    arrs.push getSequence arrs.-1
  sum arrs.map .-1

log 'Part 1', sum lines.map predict
log 'Part 2', sum lines.map(.reverse()).map predict
```


## Day 8: Haunted Wasteland ⭐⭐

```ts
{ log, getInput, getLcm, keys } from ../utils.civet

input := getInput import.meta.url |> .split '\n\n'
insNr .= 0
getIns := => input.0[insNr++ % input.0.length] is 'L' ? 0 : 1
nodes := {}

for node of input.1.split('\n').map .match /\w+/g
 nodes[node.0] = node[1..]

function movesNr(startNode: string, endNodes: string[])
  return .= 0
  name .= startNode
  node .= nodes[startNode]
  until name is in endNodes
    return++
    name = node[getIns()]
    node = nodes[name]

log 'Part 1', movesNr 'AAA', ['ZZZ']

startNodes := keys(nodes).filter .endsWith 'A'
endNodes := keys(nodes).filter .endsWith 'Z'

log 'Part 2', getLcm startNodes.map (start) => movesNr start, endNodes
```


## Day 7: Camel Cards ⭐⭐

```ts
{ log, getLines, int, sum, desc } from ../utils.civet

type Hand
  cards: string
  bid: number
  type: number

lines := getLines import.meta.url

log 'Part 1', sum getWins(lines, false)
log 'Part 2', sum getWins(lines, true)

function getWins(lines: string[], joker: boolean)
  lines
    .map (line) => parseHand(line, joker)
    .sort compareHands
    .map (hand, i) => hand.bid * (i + 1)

function parseHand(line: string, joker: boolean): Hand
  [cards, bid] .= line.split ' '
  cards: fixCardsStrength cards, joker
  bid: int bid
  type: getType cards, joker

function getType(cards: string, joker: boolean)
  counter := count cards.replaceAll joker ? 'J' : '', ''
  if joker then (counter.0 ?= 0) += cards.match(/J/g)?.length ?? 0
  switch counter
    [5] 6
    [4, 1] 5
    [3, 2] 4
    [3, 1, 1] 3
    [2, 2, 1] 2
    [2, 1, 1, 1] 1
    [...] 0

function count(cards: string)
  counter: Record<string, number> := {}
  for card of cards.split('')
    (counter[card] ?= 0)++
  Object.values(counter).sort desc

function compareHands(a: Hand, b: Hand)
  a.type is b.type
    ? a.cards > b.cards ? 1 : -1
    : a.type - b.type

function fixCardsStrength(cards: string, joker: boolean)
  cards
    .replaceAll 'A', 'E'
    .replaceAll 'K', 'D'
    .replaceAll 'Q', 'C'
    .replaceAll 'J', joker ? '1' : 'B'
    .replaceAll 'T', 'A'
```


## Day 6: Wait For It ⭐⭐

```ts
{ log, getLines, toNumbers, toNumber, multiply } from ../utils.civet

lines := getLines import.meta.url

function timesWins(time: number, record: number)
  return .= 0
  for t of [1...time]
    return++ if time - t > record / t

log 'Part 1', multiply for i of [0...4]
  timesWins
    toNumbers(lines.0)[i],
    toNumbers(lines.1)[i]

log 'Part 2', timesWins
  toNumber lines.0.replaceAll ' ', ''
  toNumber lines.1.replaceAll ' ', ''
```


## Day 5: If You Give A Seed A Fertilizer ⭐⭐

```ts
{ log, getInput, toNumbers, inRange, chunk, min } from ../utils.civet

input := getInput(import.meta.url).split '\n\n'
seeds := toNumbers input.0
mappings := input[1..].map &.split('\n')[1..].map toNumbers

function translate(nr: number, map: number[][])
  range := map.find (map) => inRange map.1, nr, map.1 + map.2
  range ? nr + range.0 - range.1 : nr

function getMin(seeds: number[])
  min .= Infinity
  for let seed of seeds
    for map of mappings
      seed = translate seed, map
    min = seed if seed < min
  min

log 'Part 1', getMin seeds
log 'Part 2', min chunk(seeds, 2).map [start, length] =>
  getMin [start...start + length]

// omptimzed part 2 -> src/05/index2.civet
```


## Day 4: Scratchcards ⭐⭐

```ts
{ log, getLines, sum, toNumbers, values } from ../utils.civet

lines := getLines import.meta.url

function wins (line: string)
  [wins, my] := line.split(':').1.split('|').map toNumbers
  my.filter(& is in wins).length

log 'Part 1', sum for line of lines
  wins line |> (n) => n ? 1 << n - 1 : 0

cards := {}
for i in lines
  cards[i] = 1

for line, i of lines
  for j .= i + 1; j <= i + wins line; j++
    cards[j] += cards[i]

log 'Part 2', sum values cards
```


## Day 3: Gear Ratios ⭐⭐

```ts
{ log, getLines, sum, flatten, values } from ../utils.civet

lines := getLines import.meta.url
parts: Record<string, number[]> := {}

for line, y of lines
  for m of line.matchAll /\d+/g
    for y of [y - 1..y + 1]
      for x of [m.index - 1..m.index + m.0.length]
        unless /[0-9.]/.test lines[y]?.[x]
          (parts.`${x},${y}` ?= []).push +m.0

log 'Part 1', sum flatten values parts
log 'Part 2', sum values(parts).map (x) => x.0 * x.1 ?= 0
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
