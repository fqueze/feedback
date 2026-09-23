## Machine CPU over a window of a resource-usage profile needs a script

- Question: "was the machine saturated between t=190s and t=240s?"
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` (after `zoom push 190,240`), then a Python script to average `cpuPercent` per 2 s bucket.
- Expected: `counter list` / `counter info` to cover it, or `thread markers --search "name:CPU Use"` to print min/avg/max of `cpuPercent` over the view.
- Got: `counter list` says "No counters in this profile". The CPU data exists only as 100 ms `CPU Use` markers, and the aggregated view gives only their durations.

## First marker in a series that lacks a value

- Question: "when did device process xpcshell9 (pid 31299) disappear from the harness's `get_process_list` DEBUG markers?"
- Command: `thread markers --search 31299 --list` shows the markers that contain the pid, but not the first later `get_process_list` marker that lacks it. I needed `--json` and a script to list the xpcshell slots in each marker.
- What would have answered it: a negated search scoped to a name, such as `--search "name:DEBUG,get_process_list,-31299"` (as it is, comma ORs the positive terms), shown in time order after the last match.

## `marker info --json` records have `handle: null`

- Command: `profiler-cli marker info m-169 m-172 --json`
- Expected: each record carries its `m-N` handle.
- Got: `"handle"` missing or null on the records, so I matched them by start time instead.

## (review) Counting a job's `test` markers by status over a time window needs a script

- Question: "how many tests ended TIMEOUT in this job, and did they all start in one burst?"
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then Python grouping `data.status` and start times.
- What would have answered it: the aggregated `thread markers --search name:test` view broken down by the `status` payload field (count, first/last start, min/max duration per status).

## (review) `profile-link.py` refuses a session loaded from a profiler.firefox.com link

- Command: `profile-link.py --session <s> --marker m-7` on a session loaded from the report's `https://profiler.firefox.com/from-url/...` link, as review-brief.md tells reviewers to load.
- Expected: a link, since the from-url link wraps the raw artifact URL.
- Got: "A link needs the raw artifact URL". Workaround: stopped a session and loaded the raw Taskcluster URL a second time, only to build one link.
