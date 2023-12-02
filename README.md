Lang: [Civet](https://civet.dev)

# Advent of Code 2023

Something is wrong with global snow production, and you've been selected to take a look.
The Elves have even given you a map; on it, they've used stars to mark the top fifty
locations that are likely to be having problems.

## Day 1: Trebuchet?! ⭐⭐

```ts
{ getLines, log, add } from ../utils.civet

lines := getLines import.meta.url

function parseValue (line: string)
  nr := line.replace /[a-z]/g, ''
  parseInt nr.0 + nr.-1

function convertWords (line: string)
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

log 'Part 1', lines.map(parseValue).reduce add 
log 'Part 2', lines.map(convertWords).map(parseValue).reduce add
```
