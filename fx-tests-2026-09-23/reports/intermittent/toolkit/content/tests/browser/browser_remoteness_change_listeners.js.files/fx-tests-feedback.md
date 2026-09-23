## Question: which retention path did the leak checker print, for each failure of this test?

- Command: `fx-tests test toolkit/content/tests/browser/browser_remoteness_change_listeners.js --task-ids --limit 0` (and `fx-tests task <id>`), then a script over each task's `mochitest-browser-chrome_errorsummary.log`.
- Expected: for a `leaked window until shutdown` failure, the `stack` field that ShutdownLeakPathFinder writes (the CC retention path, or "no path to root found (garbage cycle)"), grouped with counts, e.g. under Issues or with `--messages`.
- Got: only the message. The path is the discriminating part of a shutdown-leak failure (here: 156/156 "garbage cycle" vs. "Promise [root, 1 unknown ref(s)]" seen on macOS for a sibling test), and it was only reachable by downloading 99 errorsummary files.
- Workaround: loop over task ids, curl each errorsummary, filter `subtest == "Shutdown"` and print `stack`.
