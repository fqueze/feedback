## Marker list timestamps lose all sub-second precision past 60 s
- Command: `profiler-cli thread markers --search "..." --list --limit 0 --session browser_resources_reflows.js-2`
- Expected: timestamps precise enough to order markers, e.g. `t=65.020s`, or better `t=65019.92ms`.
- Got: `t=1m5s` on every marker after 60 s, so a poll, a packet receipt and the next poll all read `t=1m5s`. Below 60 s it is `t=48.513s`, which still can't order two markers 0.3 ms apart.
- Workaround: `--json` and read `start`, or `marker info --json` per handle.
- Question it blocked: "did the poll run before or after this IPC message arrived?" The list could show ms with 2–3 decimals, relative to the profile start.

## Question: "how regular is this repeating marker?" (the gaps between consecutive markers of one name)
- Command: `profiler-cli thread markers --search "setTimeout() for nonfunction" --list --limit 0 --json | python3 ...`, which computes min/median/max of the gaps between consecutive starts.
- The aggregate view's "Frequency Analysis" gives gap stats only for the top 5 names, not for a `--search` match of a specific task name. Showing the gap min/median/max for whatever `--search` matched would have answered it with no script.

## Question: "interleave markers from several threads chronologically"
- I wrote a script that selects each thread in turn, runs `thread markers --search X --list --json`, and merges by start time. The aim was to see a parent poll, a child's IPC send and the parent's receive in one ordered list.
- `profile markers --search` sweeps all threads, but I couldn't restrict it to 3 threads and a time range and get one flat chronological list. A `--thread t-0,t-57,t-58 --list` on `profile markers`, honouring zoom, would do it.

## `marker info --json` with several handles drops the handle
- Command: `profiler-cli marker info m-250 m-251 m-236 --json`
- Got: records whose `handle` field is missing (None), so I had to rely on the order to tell which record is which.
