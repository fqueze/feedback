## Question: "how long did the synchronous call between these two log lines take?" (browser_utility_audioDecodeCrash.js)

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0`
- Expected: timestamps precise enough to tell 1 ms from 90 ms between consecutive INFO markers (the key observation: `ProcessTools.crash()` took <1 ms when the dump was missing, 50-90 ms when it worked).
- Got: `t=4.899s` / `t=4.900s`, millisecond resolution. Workaround: `marker info m-A m-B --json` and read `.markers[].start`.
- A `--precise` option, or printing the delta to the previous listed marker, would answer it without JSON.
