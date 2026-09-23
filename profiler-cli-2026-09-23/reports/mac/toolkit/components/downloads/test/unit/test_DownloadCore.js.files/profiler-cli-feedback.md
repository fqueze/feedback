## Question: "what was the whole machine's CPU during this test?"

- Command: `profiler-cli thread markers --session <ru> --search "name:CPU Use" --list --limit 0` after `zoom push <test marker>` on a resource-usage profile.
- The list prints one row per 100 ms `CPU Use` marker without its `CPU Percent` value, so answering needed `--json` and a script averaging `data.cpuPercent` per 10 s bucket.
- What would have answered it: the CPU percent in the list row (it is the marker's only interesting field), or a summary (min/avg/max CPU Percent) in the aggregate `thread markers` view for the zoomed range.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <url> --session <id>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear suggestion to set `PROFILER_CLI_SESSION_DIR`. Cost one retry; the message was good.

## Question: "how long after each X did the matching Y come?" (save complete -> DownloadDoneResolve)

- Command: `profiler-cli thread markers --session <s> --search "name:DownloadPlatform::DownloadDoneResolve,name:BackgroundFileSaver::NotifySaveComplete,Starting" --list --limit 0`
- The list has the rows; pairing each resolve with the preceding save, and summing the gaps per subtest, needed a script over the text output (`gaps.py` in this directory).
- What would have answered it: a "gaps between consecutive markers matching A then B" view, or at least a per-row delta-from-previous-row column in `--list`.
