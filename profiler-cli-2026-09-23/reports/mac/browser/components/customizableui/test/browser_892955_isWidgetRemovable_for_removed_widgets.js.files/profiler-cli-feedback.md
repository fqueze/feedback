## Question: which tests ran in this browser session, in order, with their status and duration?

- Command: `profiler-cli thread markers --category Test --search "browser/components/customizableui/test/" --list --limit 0 --session b892955-1`
- Expected: a way to list only the per-test `test` interval markers (one row per test file: PASS/FAIL, start, duration), which is the first thing to read in a per-test profile to see what ran before the failing test.
- Got: every TEST-PASS / INFO line of every test (hundreds of rows), because the test path is in all their payloads. `--search name:test` also matches TEST-PASS/TestUtils (substring on `name`, which is also a payload key), and `--search status:PASS,status:FAIL` matches the TEST-PASS markers too (they carry a `status` field).
- Workaround: `--json | jq '.flatMarkers[] | select(.name=="test")'`.
- What could have shown it: an exact-name filter (e.g. `name=test` or `--name test`), or a `thread markers --tests` summary listing the `test` markers.

## Question: when does a refresh-observer reason start and stop over the session?

- Command: `profiler-cli thread markers --search "Synthetic mouse move" --list --limit 0 --session b892955-1`
- Expected: a per-time-bucket count (like `counter info`'s "over time" section) for the matching markers, to see where a 60 Hz tick train begins.
- Got: 2,500+ rows.
- Workaround: `--json | jq` + awk bucketing by second.
- What could have shown it: an "over time" histogram for a marker search (`--histogram 1s` or similar).

## Question (review): does a periodic marker train ever pause, and during a pause is the marker absent or does nothing tick at all?

- Command: `profiler-cli thread markers --search "RefreshDriverTick" --list --limit 0 --session review-892955-1b` (about 2,500 rows after 16.6 s)
- Expected: a way to list the gaps longer than N ms between consecutive matching markers, together with whatever non-matching markers of the same name fall inside each gap. That tells a missing tick reason apart from a blocked main thread.
- Got: every row. I needed `--json` plus a Python script to find the 0.3–0.55 s gaps after each failure and to check that no tick of the main window fell inside them.
- What could have shown it: a `--gaps <ms>` option on `thread markers` (or an "over time" section, as in the entry above) that reports the start, end, and length of each gap.
