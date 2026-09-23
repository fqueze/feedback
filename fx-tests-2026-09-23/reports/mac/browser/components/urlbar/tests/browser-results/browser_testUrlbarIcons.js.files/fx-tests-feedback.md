## `--bugs` prints nothing when the bug names the test's pre-rename path

- Command: `fx-tests test browser/components/urlbar/tests/browser-results/browser_testUrlbarIcons.js --bugs`
- Expected: bug 2053321 ("Intermittent browser/components/urlbar/tests/browser/browser_testUrlbarIcons.js | single tracking bug"), since the test moved from `browser/` to `browser-results/` on 2026-07-22; or at least a "no bugs found" line.
- Got: the usual output with no bug section and no line saying none was found. `fx-tests intermittent --bug 2053321` itself lists 11 annotated jobs naming the new path.
- Workaround: Bugzilla quicksearch on the file name.

## Issues are not grouped when the message differs only by a `blob:` UUID

- Command: `fx-tests test browser/components/urlbar/tests/browser-results/browser_testUrlbarIcons.js`
- Expected: one issue, "Icon is search glass when urlbar is unfocused - "blob:system/<uuid>" == ...", counted 124x.
- Got: 125 separate "1x" issues ("↑ same as 2, but - "blob:system/d29ec865-…""), which makes the Issues list read as many failure modes and hides the counts.
- Workaround: `--json` and a script over `taskIds[].message`.

## Question: which push was the last to fail, and which landing stopped it?

- Command: `fx-tests test <path> --task-ids --limit 0` (and `--json`).
- The question: "the last failing push for this test, and the first later push where it ran and passed", to bracket the landing that stopped a failure that no longer happens.
- Got: task ids per day, with no repo, revision or push time; I queried `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>` for `GECKO_HEAD_REPOSITORY` / `GECKO_HEAD_REV` on each of the 22 last-day tasks, then hg json-pushes.
- What could have shown it: repo and revision per task id, and for `--history`, the last failing push and first passing push after it.
