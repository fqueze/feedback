## Question: "in which order, and how far apart, did the test's log lines happen within one second?"

- Command: `profiler-cli thread markers --category Test --search browser_console_many_toggles --list --limit 0 --session <s>` on a 2m40s profile.
- Expected: start times with millisecond precision (e.g. `t=69.570s`), since the whole test body (10 open/close steps) runs within 2.3 s.
- Got: `t=1m10s` / `t=1m11s` for every row, so the 0/0.1/0.2 s waits and the gaps between "Open" and "Close" cannot be read.
- Workaround: `--json` and print `start/1000` with a script.

## Question: "why did my session vanish?"

- Command: `profiler-cli thread info --session browser_console_many_toggles.js-1` (after `zoom pop` / `zoom push 69.5,72.5`), about 5 minutes after load.
- Expected: thread info, or an error saying the daemon died and why.
- Got: `Unknown session browser_console_many_toggles.js-1: no metadata found`. The session's `.log` ends with `Received message: command` and no error; the metadata file was gone. Machine had about 2 GB available at load time (other agents' sessions), so probably killed under memory pressure, but nothing says so.
- Workaround: reload the profile under a new session name; all marker handles changed.

## Question: "what happened, in order, between the test's `Close` and the window's destruction?" (same rounding as above)

- Command: `profiler-cli thread markers --session <s> --search "devtools-server.js,domwindowopened,web-console-destroyed,toolboxComponentError,fluent-l10n.js,name:RDP Front" --list --limit 0`
- Expected: one chronological list with ms times; the decisive gap here was 1.6 ms (fluent-l10n.js load at 70.1026, `Close` at 70.1042).
- Got: every row `t=1m10s`.
- Workaround: `--json` and a script printing `start/1000` to 4 decimals, merged with the test-log list. Needed on all three profiles.

## Question: same as above, "in which order did these markers happen within a few ms?" (review-browser_console_many_toggles.js, 2026-09-22)

- Command: `profiler-cli thread markers --session <s> --search "…" --list --limit 0` on the same three profiles, to check a report's 4-decimal times (e.g. `Close` at 41.9582 against `Perform microtasks` ending at 41.9580).
- Got: rows rounded to the second again. The same `--json` plus `start/1000` script was needed for every check in all three profiles.
