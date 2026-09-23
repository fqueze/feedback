## `thread markers --list` rounds times to the second in profiles over a minute long

- Command: `profiler-cli thread markers --session <s> --category Test --search <test file> --list --limit 0` on a 7m31s profile
- Expected: sub-second start times, as for the first minute (`t=9.640s`).
- Got: `t=7m22s`, `t=7m23s`, `t=7m31s`: every marker of the test in two or three buckets, so neither the order within a second nor the length of the end-of-test wait (8.46 s between "checking for open popups" and the vsync failure) can be read.
- Workaround: `profiler-cli marker info m-N --json` per marker, reading `start`.
## `load` of a large CI profile times out after 60 s, while the daemon keeps loading (review)

- Command: `profiler-cli load '<profiler.firefox.com from-url link to Xh6MnwM2R3OFkWAeuPuZLA profile_browser_ext_webNavigation_urlbar_transitions.js.json>' --session <s>`
- Expected: the load to finish, or to say the daemon is still loading and how to wait for it.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, yet `session list` shows the session and `status` answers `Profile still loading, try again shortly`. The error reads as a failure; it is not one.
- Workaround: poll `profiler-cli status --session <s>` until it answers.
