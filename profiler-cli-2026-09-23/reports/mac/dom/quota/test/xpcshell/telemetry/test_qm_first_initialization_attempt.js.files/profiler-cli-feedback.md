## `thread samples` keeps the previous zoom's result after `zoom pop` / `zoom clear`

- Session: `test_qm_first_initialization_attempt.js-3`, profile
  `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/M6DMdp9CTYSt3Rxb3Pn7zA/runs/0/artifacts/public/test_info/profile_test_qm_first_initialization_attempt.js.json`
- Commands: `zoom push m-1` (a 6.379 s Jank), `thread samples --include-idle` (358 samples),
  `zoom clear`, `thread samples --include-idle`; then `zoom push m-5` (3.589 s),
  `thread samples` (202), `zoom pop`, `thread samples` and `thread samples --include-idle`.
- Expected: after the pop/clear, the full-profile counts (`thread info` says the thread has 1691 samples).
- Got: the header says `View: Full profile`, `status` says `View range: Full profile`, but the
  categories and function totals are exactly those of the last zoomed range (358, then 202).
  Silent: nothing marks the numbers as stale, so a "99% of the profile is in X" conclusion drawn
  from them is wrong.
- Workaround: none found in-session besides reasoning from markers instead of sample counts;
  reloading the profile presumably resets it.

## Loading fails in a sandbox until `PROFILER_CLI_SESSION_DIR` is set

- Command: `profiler-cli load <url> --session <id>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'` — the message did
  name the fix (`PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`), so this
  cost one call. Could default to `$TMPDIR/profiler-cli` when the home dir is not writable.

## Same stale-range problem with `thread functions`, on a second profile

- Session `test_qm_first_initialization_attempt.js-1` (profile
  `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/TOOEp2dYTCy648MvEY27bw/runs/0/artifacts/public/test_info/profile_test_qm_first_initialization_attempt.js.json`).
- `zoom push m-766` then `thread functions --search crossbeam --include-idle`: "1 functions
  (filtered from 23)". `zoom pop`, same command: header `View: Full profile`, still "filtered
  from 23", the same one function. Before that, after a `zoom push 29.9,30.31` / `zoom clear`,
  the full view said "filtered from 14" and found no `crossbeam`/`sqlite` frame, although the
  m-766 range alone contains both.
- So a search over "the whole profile" after any zoom silently answers for the last zoom only,
  and a "not found" there is false.

## Question needing a script: "which tests ran at the same time as this one"

- Profile: the resource-usage profile of TOOEp2dYTCy648MvEY27bw.
- Command: `thread markers --search "name:test" --list --limit 0 --json`, then a Python
  script that intersects every `test` marker's [start, start+duration] with the timed-out test's,
  and counts the open ones every 3 s.
- The question: how loaded the parallel phase was while my test ran (how many tests were running
  at once, and which long ones overlapped), when the profile has no CPU counter.
- What the default output could show: an "overlapping" filter on markers (`--overlapping m-N`),
  or a concurrency count next to each `test` marker in the list.

## (review) A resource-usage profile's machine CPU is in markers, and nothing points there

- Profile: the resource-usage profile of TOOEp2dYTCy648MvEY27bw (and of M6DMdp9CTYSt3Rxb3Pn7zA).
- Commands: `profile info` says "CPU activity over time: No significant activity."; `counter list`
  says "No counters in this profile." Only `thread markers --group-by name` shows 16,249
  `CPU Use` markers, one per ~100 ms, each with `CPU Percent` and `Idle %` for the whole machine.
- Cost: the report's author concluded "how busy the machine was cannot be read" from the first
  two, and the reviewer found the load, 100 % for the whole timed-out run, only by grouping.
- Expected: `profile info` on a profile whose CPU is in `CPU Use` markers says so, or summarises
  them as it does a counter.

## (review) Question needing a script: "how busy was the machine during this test marker"

- Commands: `zoom push m-1` (the TIMEOUT `test` marker), then
  `thread markers --search "name:CPU Use" --list --limit 0` lists 306 rows with only a duration
  and no value; the aggregate view gives only their count and interval. Reading them took
  `--json` and a Python script (mean, min, share at >= 99 %), or `marker info m-a..m-b` for a
  handful.
- What the default output could show: the `CPU Percent` field in each list row, or a min / mean
  / max of numeric fields in the aggregate view (as `counter info` does per bucket).

