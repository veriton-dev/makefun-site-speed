# veriton-dev/makefun-site-speed

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/veriton-dev/makefun-site-speed/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/veriton-dev/makefun-site-speed/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for **makefun-site 1.0.0**, the reference static site generator of make.fun's
"Build a static site faster, 22 November 2026" bounty. `run(input)` in `solve.js` returns the output tree
makefun-site 1.0.0 builds for the same input — every path, every byte — while spending less CPU on it.

## What is different

Nothing about the output, which is the point: the rules are makefun-site's own and the code here is a second
implementation of them. The speed comes from where the time actually went, measured rather than guessed:

| change | why it is faster |
|---|---|
| HTML escaping by one native `replace`, with a fast path for text holding none of the five characters | a per-character JavaScript loop measured 2× slower on mixed text and 50× slower on plain prose |
| short escaped strings memoised (titles, urls, tag names, dates) | one build escapes the same few hundred short strings thousands of times |
| slugs and long dates memoised | a tag's slug is asked for once per post that carries it |
| word counts by one `match` over the body, computed once per source | the count was recomputed for every list, tag page and page showing the same post |
| bulk copies in the inline renderer, with an early return for text holding no inline syntax | most of a page's text is prose that Markdown does not touch |
| index loops over `forEach`/`map` closures in the block lexer and the template renderer | the renderer walks a few million nodes per case |

## The rules it follows

`run(input)` implements makefun-site 1.0.0's documented behaviour exactly: its front-matter subset and its refusal
reasons in order, its own Markdown dialect, its template dialect (`{{ }}`, `{{{ }}}`, `for`, `if`/`else`, `include`,
standalone-tag lines, the include depth limit), its permalinks and reserved directories, its post ordering with
newer/older links, its tag grouping and ordering, its index and tag pagination, its static-file shadowing note, and
its `build-report.txt` line for line.

## Verification

- Byte-identical to the house oracle on the kit's practice set (3 cases) and on 24 generated cases drawn from the
  kit's own generator streams — compared as canonical JSON, every path and every character.
- Faster than the house baseline on the same work: 2.4–2.6× on the practice set, measured as CPU time over repeated
  runs in one process.

## Run it

```sh
node /bot/main.ts   # reads /input/cases.json, writes /output/answers.json
```

## Files

- `solve.js` — the implementation, exporting `run(input)`.
- `main.ts` — the driver the harness runs.

## License

MIT.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>
