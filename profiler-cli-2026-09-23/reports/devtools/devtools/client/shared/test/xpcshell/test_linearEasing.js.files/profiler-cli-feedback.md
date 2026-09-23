# profiler-cli feedback (test_linearEasing.js)

## Question: "was the machine CPU-saturated during this time window?" (xpcshell resource-usage profile)

- Command: `profiler-cli profile info --session S` and `profiler-cli counter list --session S` on
  `.../GPvPpgapSOKDrU4lDTyD8Q/runs/0/artifacts/public/test_info/profile_resource-usage.json`
- Expected: a CPU-use track or summary, like the brief's "CPU use of the whole machine".
- Got: `profile info` says "CPU activity over time: No significant activity."; `counter list` says
  "No counters in this profile." The CPU data exists only as ~11,500 `CPU Use` interval markers,
  each with a `CPU Percent` field, and `thread markers --list` does not show field values.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` to collect
  handles, then `marker info <handles>` piped through grep/awk to average `CPU Percent` per
  zoomed range (repeated for ~12 ranges).
- What would have answered it: `profile info` (or `thread markers` on a zoomed range) summarizing
  `CPU Use` markers as a per-bucket mean/min/max CPU %, the way `counter info` does "over time".

## Question: "how many tests were running in parallel at time t?" (review-test_linearEasing.js)

- Command: `profiler-cli zoom push 216,231` then `thread markers --search "name:test" --list --limit 0`
  on the GPvPpgap resource-usage profile.
- Expected: a count of the `test` interval markers that overlap a given instant or range.
- Got: a list of every `test` marker that intersects the range, with start and duration only.
  Counting the overlaps at one instant took `--json` and a script (the answer is 30).
- What would have answered it: `thread markers --at <t>` (the markers open at that instant), or a
  "max concurrent" column in the aggregated `thread markers` view.

## profile-link.py refuses a session loaded from a profiler.firefox.com link (review-test_linearEasing.js)

- Command: `profiler-cli load "https://profiler.firefox.com/from-url/…/marker-table/?marker=10443&thread=0&v=17" --session S`,
  then `profile-link.py --session S --marker m-44`.
- Expected: a link. The review brief says to open the report's links with `profiler-cli load <link>`.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself". I had to reload the
  whole profile from the raw Taskcluster URL just to build two links.
- Suggestion: have profile-link.py pull the `from-url` artifact URL out of a profiler link.
