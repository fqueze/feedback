# fx-tests feedback — browser_dbg-javascript-tracer-sidebar.js

## `--since` takes a day count, not a date

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-sidebar.js --since 2026-09-12 --coverage`
- Expected: the window from that date on, as `--day` accepts a date.
- Got: `fx-tests: --since expects a non-negative integer, got "2026-09-12"`.
- Workaround: counted the days back by hand (`--since 10`).

## Question: runs and failures per config before and after a fix landed

- Question: "how many runs did the failing configs have before the fix date, and how many since, with how many failures in each" — the evidence for a `no longer fails` verdict.
- Commands: `fx-tests test <path> --coverage --limit 0` and `fx-tests test <path> --since 10 --coverage --limit 0`, then subtracting the second from the first per config.
- What could show it: a `--split <date>` (or `--history --config <substring>`) giving per-config pass/fail counts on each side of a date.
