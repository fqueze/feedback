## `thread markers --list` rounds times to whole seconds past one minute

- Command: `profiler-cli thread markers --category Test --search browser_ext_connect_and_move_tabs.js --list --limit 0 --session <s>`
- Expected: millisecond times, as shown below one minute (t=9.640s).
- Got: `t=1m15s`, `t=1m16s`, `t=1m24s` for markers 0.7 s and 8 s apart; the order of events inside one second is lost.
- Workaround: `profiler-cli marker info m-1 m-15 ... --json` and read `start`.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <taskcluster url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear suggestion to set PROFILER_CLI_SESSION_DIR. The suggestion worked; noting it only because it cost one load attempt.

## Question: which document do these markers belong to? (review)

- Command: `profiler-cli thread markers --search 'waiting for paint' --group-by innerWindowID --session <s>`
- Expected: one group per innerWindowID, or an error saying the key is unknown and `field:innerWindowID` is meant.
- Got: `(no value): 4341 markers`, which reads as "these markers carry no document". The report under review concluded exactly that. `--group-by field:innerWindowID` gives the right answer (4338 on 4294967338, 1 each on 3 others). Separately, `--search innerWindowID:4294967338` matches 0 markers, although `marker info` lists `innerWindowID: 4294967338` under "Fields (raw payload, no schema)".
- Workaround: `--group-by field:innerWindowID`.
- What could have shown it: reject or warn on a bare `--group-by` key that no marker has, and let `field:value` search match raw payload fields such as `innerWindowID`.
