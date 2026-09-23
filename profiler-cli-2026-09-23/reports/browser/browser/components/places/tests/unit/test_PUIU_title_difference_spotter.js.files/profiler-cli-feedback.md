## Question: how busy was the machine, second by second, over a range of a resource-usage profile?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (after `zoom push 30,60`)
- Expected: the CPU percentage on each row (it is the only field that matters for a `CPU Use` marker), or a summary over the zoomed range.
- Got: rows with time and duration only; the value is only in `marker info` one marker at a time. Resource-usage profiles have no counters (`counter list` says none), so `counter info` cannot answer it either.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` per second.
- What could have shown it: the `cpuPercent` field on the `--list` row, or `thread markers --search "name:CPU Use"` aggregate stats (min/avg/max of the value) over the zoom.

## `zoom push 1m37,2m12` silently zooms to 1s-2s

- Command: `profiler-cli zoom push 1m37,2m12 --session <s>`
- Expected: either the 1m37s-2m12s range (the format the tool itself prints times in), or an error.
- Got: `View: ts-1→ts-2 (1s)` with no warning; the following marker query answered for the wrong window.
- Workaround: seconds (`97,132`).

## Question (review): link a new observation from a session opened on a report's profiler link

- Command: `profiler-cli load '<profiler.firefox.com/from-url/...?marker=3169&thread=0&v=17>' --session S`, then `python3 profile-link.py --session S --marker m-292`
- Expected: a link, since the review brief says to open the report's links with `profiler-cli load <link>`, and the session knows the underlying artifact URL (it decoded it to load).
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.` (exit 1).
- Workaround: stop the session and reload the same profile from the decoded Taskcluster URL, a second 0.6 GB load.
- What could have shown it: profile-link.py (or the session) extracting the `from-url` artifact URL from a profiler link it was loaded from.
