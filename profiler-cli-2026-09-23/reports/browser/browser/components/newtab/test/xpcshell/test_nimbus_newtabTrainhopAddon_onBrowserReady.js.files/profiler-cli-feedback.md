## Question: "does this link's `marker=N` point to the marker the report quotes?"

- Command: `profiler-cli load '<profiler.firefox.com link with marker=N>' --session S`, then `thread markers --search … --list`, then `marker info m-a m-b … --session S --json` piped through a Python script that prints `markerIndex`.
- Expected: `load` of a link that has `marker=N` printing the marker it selects (handle, name, time). Or `marker info` text output showing the marker's index.
- Got: `load` prints only the selected thread and range. The text output of `marker info` has no index, so `--json` plus a script was the only way to check each link.
- Workaround: the script over `marker info --json`.
