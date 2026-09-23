## Question: what were the machine's IO wait and write throughput, over the job and during one stall? (resource-usage profile; same as the sibling report's entry)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` and `--search name:IO ...`, then a Python script (`iostats.py`) to average `iowait_pct` and `write_bytes`, over the job and over a `zoom`-like window.
- Expected: `counter info`, or a marker-field aggregate (e.g. `thread markers --search "name:CPU Use" --stats iowait_pct`), that honours `zoom push`.
- Got: no counters in resource-usage profiles, and no aggregates of payload fields. I needed it for 23 jobs, so I ran the script 23 times.

## Minor: `marker info --json` has no handle

- Command: `profiler-cli marker info m-16 m-121 --session ... --json`
- Expected: each record to carry its `handle` (m-16, m-121), so the output of several handles can be told apart.
- Got: no `handle` key; I relied on the order.

## (review) Loading a raw Taskcluster profile URL selects a content thread, and queries then silently find nothing

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/aaRG8tN4TIKTmHNxrgVHhQ/runs/0/artifacts/public/test_info/profile_browser_translations_select_context_menu_feature_disabled.js.json" --session ...`, then `thread markers --category Test --search <test> --list`
- Expected: the parent process GeckoMain (t-0) selected by default, as when loading a profiler.firefox.com URL with `thread=0`, or at least the selected thread printed in the query's "No markers" result.
- Got: `t-20 (GeckoMain, WebExtensions)` selected. Both queries printed only "No markers match the specified filters.", which reads as "the stall is not there".
- Workaround: `status`, then `thread select t-0`.

## (review) Payload-field aggregates over a zoom: needed again

- Same question as the entry above: IO wait and write MB/s during one stall, plus writes per 5 s window. Ran `thread markers --search "name:CPU Use" / "name:IO" --list --limit 0 --json` and a Python script, for 3 more profiles.
