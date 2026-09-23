## Question: when did a logged console message actually happen?

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0` on an xpcshell per-test profile.
- Expected: `CONSOLE_MESSAGE` markers placed at the time the message was emitted.
- Got: they are placed when the harness logged them, a few ms late, so `Cancelling postponed install` appears after the TEST-PASS that follows it. The message's own epoch-ms timestamp is in the text.
- Workaround: `profile meta` for the start time, then `(epoch - start)` by hand; or use a stack-bearing marker (`NotifyObservers flush-cache-entry` from `cancel`) as the time anchor.
- What would have answered it: an option to show, for Log/Text markers that carry an epoch timestamp, the profile-relative time of that timestamp.

## Question: machine CPU during one test's run (same as the sibling report's entry)

- Command: `zoom push <range>; thread markers --search "CPU Use" --list --limit 0 --json`
- Expected: the average CPU in the default output.
- Got: durations only. Workaround: `cpuavg.py`.
