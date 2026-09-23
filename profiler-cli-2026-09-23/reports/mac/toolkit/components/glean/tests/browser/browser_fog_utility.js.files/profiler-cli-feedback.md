## `thread markers --search` does not match the text it displays for Glean metric markers

- Command: `profiler-cli thread markers --search 'fog.max_pings_per_minute' --list --limit 0 --session <s>`
  (also `fog.initializations`, and both joined with a comma).
- Expected: the `Quantity::set` markers whose list row reads `fog.max_pings_per_minute : 15`.
- Got: "No markers match the specified filters." `--search max_pings_per_minute` finds them. The
  row text is built from two payload fields (`Category: fog`, `Metric: max_pings_per_minute`),
  and each field is matched on its own, so the dotted identifier shown in the row, which is also
  the metric's name in Glean's YAML and in test code, never matches.
- Cost: a comma-OR search that combined a dotted metric id with other terms came back empty, and
  I nearly read that as "no FOG init before the first reset" when the markers were there.
- Workaround: search the part after the dot only. Better: also match the rendered row text, or
  at least say in the empty result that the fields are matched one at a time.

## `load` of a raw Taskcluster URL selects a content process's main thread, and searches on it look like absence (review-browser_fog_utility.js)

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/fVQSJdrKTaKUPlc3ICY5mQ/runs/0/artifacts/public/test_info/profile_browser_fog_utility.js.json' --session <s>`, then `thread markers --search testResetFOG --list --limit 0 --session <s>`.
- Expected: the parent process main thread selected, as when loading a profiler link with `thread=0`, or the search to say which thread it ran on and that other threads have matches.
- Got: `Selected thread: t-14 (GeckoMain, Privileged Content)`, and eight searches in a row answered "No markers match the specified filters." Only the `[Thread: t-14 (Privileged Content)]` header, which is easy to skip, showed why.
- Cost: one round of queries, and a real risk of reading "no FOG init in this profile".
- Workaround: `thread list`, then `thread select` on the parent GeckoMain before searching. Better: default to the parent main thread for mochitest profiles, or have an empty `thread markers` result point to `profile markers --search` when other threads match.
