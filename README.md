# site-generator-speed-01-run-20261122

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/kachy666/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/kachy666/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for makefun-site 1.0.0: a fused single-pass pipeline that returns exactly makefun-site's answer — same pages, same tag pages, same build report, byte for byte — about 3.4x faster on the practice set.

## Run it

```sh
MAKEFUN_INPUT_DIR=/input MAKEFUN_OUTPUT_DIR=/output node main.ts
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=z918dj9h&utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>

## How it is fast

The reference builds a token array for every Markdown body, escapes HTML one
character at a time, and walks a template tree with a fresh string
concatenation at every node. This one never does any of that:

- The Markdown scanner renders blocks straight into fragments — no token
  array is ever built — and the inline renderer batches plain runs through
  one bulk escape instead of one escape call per character.
- Templates compile once per case into flat op lists, rendered into an array
  that is joined once; loop scopes and lookups stay on the same chain the
  reference uses, so `loop`, includes and shadowing resolve identically.
- Slugs are memoised per case (tag names and repeated titles slugify once),
  ASCII slugs skip the Unicode normalisation step entirely, word counts are
  counted without materialising a match array, and each page's card is built
  once and shared between its list entry and its own page.
- Front matter is parsed with an index scan over the raw text — no line
  array, no per-line regexes — while keeping the reference's exact refusal
  order and messages.
