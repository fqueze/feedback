# profiler-cli feedback (browser_ext_request_permissions.js, 2026-09-22)

## Question: at what time, to the millisecond, did each line of this test's log happen?

- Command: `profiler-cli thread markers --session browser_ext_request_permissions.js-1 --category Test --search browser_ext_request_permissions --list --limit 0`
- Expected: timestamps precise enough to order the test's log against the vsync wait and the
  WebExtensions process's ticks (the test ran at 214.2–223.7 s).
- Got: every one of the 141 rows reads `t=3m34s` or `t=3m35s`; past one minute, the list
  drops sub-second resolution, so the rows, the `waitForCondition` interval and the failure
  cannot be ordered or measured from the text.
- Workaround: `marker info <handles> --json` and reading `start` in ms. The list could keep
  milliseconds (`t=215.213s`, or `3m35.213s`) whatever the profile length.

## Question: over what time span does each document's refresh driver tick?

- Command: `profiler-cli thread markers --session browser_ext_request_permissions.js-1 --search 'waiting for paint' --group-by field:innerWindowID`
- Expected: per group, the count and the first and last times, which says at once that one
  document ticks from 9.8 s to the end of the profile.
- Got: counts and three example handles per group only.
- Workaround: `--list --limit 0 --json` piped to a Python script to filter by
  `data.innerWindowID` and take the first and last `start`. Printing first/last time per group
  (or the time range the group spans) would answer it.

## Question: which markers belong to this document (innerWindowID)? (review, 2026-09-22)

- Command: `profiler-cli thread markers --session review-browser_ext_request_permissions.js-1 --search innerWindowID:4294967338` (and `--search 4294967338`)
- Expected: the popup.html `DocumentLoad`, its paints and its 12,292 `RefreshDriverTick waiting
  for paint` markers, which all carry `innerWindowID` 4294967338 in `rawFields` / `data`.
- Got: `0 markers`. `--group-by field:innerWindowID` on the same thread finds that value on 12,292 markers.
- Workaround: `marker info <m> --json` and reading `rawFields` for each candidate. Matching
  `--search` against `data`/`rawFields` values such as `innerWindowID` would answer it.
