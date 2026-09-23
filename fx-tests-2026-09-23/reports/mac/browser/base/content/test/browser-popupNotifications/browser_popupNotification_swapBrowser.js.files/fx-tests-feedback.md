## Question: did this test fail before its directory was renamed?

- Command: `fx-tests test browser/base/content/test/browser-popupNotifications/browser_popupNotification_swapBrowser.js --history`
- Expected: the test's history over the 21-day window, or a hint that it only exists under this path since a given day.
- Got: 0 runs every day until 2026-09-11 and then ~100-400 a day, which reads like a new test. It is a rename (bug 2069131, `popupNotifications/` -> `browser-popupNotifications/`); the old path's failures are only visible through `fx-tests intermittent --bug 1963055 --since 21 --tree all`, which lists 5 annotations under the old name.
- What would have answered it: when a path's runs start mid-window, say so ("first seen 2026-09-11 — renamed? `--path` the old name"), or follow renames by matching the same file name in a sibling directory whose runs stop the same day.

## Repeated header on every flag

- Commands: `fx-tests test <path>`, then `--history`, then `--task-ids --limit 0`
- Got: the same ~20-line verdict and per-config table reprinted before each flag's own section. Minor, but it triples the tokens for the three commands the brief asks for in a row. A way to get only the requested section (`--only history`) or combining flags in one call would avoid it.
