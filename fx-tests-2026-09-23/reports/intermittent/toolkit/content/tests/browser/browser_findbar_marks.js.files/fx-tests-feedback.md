## `intermittent --bug` default window hides a branch that still fails

- Question: "is this bug still being starred anywhere after the trunk fix?"
- Command: `fx-tests intermittent --bug 2019094` -> exit 2, "no sheriff annotations for bug 2019094 on trunk between 2026-09-16 and 2026-09-22".
- Expected: a hint that other trees have annotations in that window (mozilla-esr153 had 8).
- Got: a generic "widen with --since, or try --tree all" hint.
- Workaround: `--since 30 --tree all --json` plus a script grouping `occurrenceRows` by tree and day. The default output could have shown a per-tree x per-day count (the `history` field is summed over trees, so it cannot show which tree kept failing).
