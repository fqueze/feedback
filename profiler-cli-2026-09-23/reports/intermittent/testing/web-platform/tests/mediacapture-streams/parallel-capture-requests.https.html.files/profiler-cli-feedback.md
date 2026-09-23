# profiler-cli feedback (parallel-capture-requests.https.html)

## Question: "which test does each FAIL marker belong to?" (resource-usage profile)

- Command: `profiler-cli thread markers --session parallel-capture-requests.https.html-1 --search NotAllowedError --list --limit 0`
- Expected: one row per marker, showing the test path and subtest.
- Got: each row is cut after `... — /mediacapture-streams/`. The long message comes first in the label, so the part that tells rows apart (the test file and the subtest) is always the part cut off. All 8 rows looked the same.
- Workaround: `--json` and a Python one-liner printing `label.split(' — ')[-1]`. For TestStatus markers, the list view could put the test path before the message, or truncate the message rather than the whole label.

## `--group-by <payload key>` silently groups everything under "(no value)" (review-parallel-capture-requests.https.html)

- Command: `profiler-cli thread markers --session review-parallel-capture-requests.https.html-2 --search NotAllowedError --group-by test`
- Question: "how many of these FAIL markers per test file?"
- Expected: one group per value of the TestStatus `test` field, or an error saying payload keys need the `field:` prefix.
- Got: `(no value): 79 markers`, no hint. `--group-by field:test` works, but I first wrote a Python script over `--list --json` before finding it.
- Suggestion: when a `--group-by` key yields no value for every marker and matches a payload key, suggest `field:<key>`.
