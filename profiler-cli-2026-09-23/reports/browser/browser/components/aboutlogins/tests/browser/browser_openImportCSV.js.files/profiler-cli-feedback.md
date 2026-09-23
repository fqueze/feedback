## Question: in which order did markers within the same second happen, in ms?

- Command: `profiler-cli thread markers --list --limit 0 --search ... --session S` (and `profile markers --search`) on a 77 s profile.
- Expected: start times precise enough to order a race that plays out in 5 ms (Spawn query received in content vs AboutLogins message received).
- Got: every row printed as `t=1m17s`; `marker info` also prints `Time: 1m17s`. Once a profile is over a minute long, the text output loses sub-second resolution.
- Workaround: `marker info m-a m-b ... --json` piped to a Python one-liner printing `start`/`end`. The default output could print `t=1m16.6963s` (or ms) like it does for profiles under a minute (`t=52.707s`).
