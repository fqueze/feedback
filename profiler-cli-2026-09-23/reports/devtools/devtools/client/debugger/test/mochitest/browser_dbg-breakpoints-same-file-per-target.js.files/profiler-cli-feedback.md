# profiler-cli feedback — browser_dbg-breakpoints-same-file-per-target.js

## Question: "which of these two threads' markers came first, to the tenth of a millisecond?"

- Command: `profiler-cli thread markers --session S --thread t-171 --search "Runnable,Awake" --list --limit 0`
- Expected: start times precise enough to order a worker thread's startup against the main thread's wake-up, which were 0.6 ms apart (and durations for the Awake intervals).
- Got: `t=2m7s` for every row, beyond the first minute: whole seconds, so every marker in the 20 ms window printed the same time.
- Workaround: a script over `--list --json` printing `start` in ms and `duration` (`ml.sh` in this directory), run on four threads.
- What the output could have shown: ms (or 0.1 ms) start times in `--list`, at least when a zoom narrower than a few seconds is active, and each row's end time.

## Question: "what did the main thread wait for between these two markers?"

- `thread samples-top-down` in a 20 ms zoom gave two samples; there is no command listing individual samples with their time and stack, so I could not tell which sample fell before or after a given marker without bisecting the zoom range by hand (three extra zoom/sample round trips).
