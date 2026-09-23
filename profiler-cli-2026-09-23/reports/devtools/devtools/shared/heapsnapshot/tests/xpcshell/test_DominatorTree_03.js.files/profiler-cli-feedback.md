## Question: which test-runner process names did the harness's `get_process_list` listings show around one moment?

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0 --json` (74 MB), then a Python script parsing each label as a Python literal.
- Expected: reading the 5–6 listings around the failure directly.
- Got: `--list` cuts each label to the terminal width, so a listing cannot be read without `--json` or `marker info`; `--search` cannot filter inside one label.
- Could have shown: a `--full-labels` option for `--list`.

## Question: in what order did markers a few ms apart happen?

- Command: `profiler-cli thread markers --list` zoomed to 817–819.3 s.
- Got: `t=13m37s` for every marker in the same second; needed `--json` `start` to order `launch_application`, the ps listing and the failure message (9 ms apart).
- Could have shown: millisecond times in `--list` when zoomed to a short range.

## `profile info` on a resource-usage profile

- Command: `profiler-cli profile info` on `profile_resource-usage.json` (Android xpcshell job).
- Got: "Platform: Unknown", one thread `t-0 (, mach)` at 0 ms CPU, "No significant activity", although the thread holds 85,551 markers including `CPU Use` intervals. Reading it as an empty profile would be wrong; a marker count there would help.

## Question: which marker index does each listed marker have? (review-test_DominatorTree_03.js)

- Command: `profiler-cli thread markers --session <s> --search <text> --list --limit 0 --json`, to check the `marker=N` of 26 links in a report.
- Expected: each `flatMarkers` entry carrying its `markerIndex`, as `marker info --json` does.
- Got: `handle`, `start`, `label`, `fields` but no `markerIndex`, so each link needed its own `marker info <m-…> --json` call (26 calls in a shell loop).
- Could have shown: `markerIndex` in `flatMarkers`, or `marker info` accepting `--json` with a comma-separated handle list returning one compact line per marker.
