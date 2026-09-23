## Ordering markers closer together than 1 ms

- Question: did the `thread.resumed` RDP marker come before or after the `MessageEventToParentRunnable` runnable in which the debugger's `paused()` action was still running? Both printed as `t=4.105s`.
- Command: `profiler-cli thread markers --search RDP --list` and `... --search Runnable --list`, then `profiler-cli marker info m-240 m-129 --json | python3 ...` to read `start`/`end` in ms (4104.568 vs 4104.586).
- Could have shown: sub-ms start times in `--list` (e.g. `t=4.1046s`), or in `marker info` text output, which gives `Time: 4.105s`. Also, `marker info --json` records carry no `handle` field, so a multi-marker JSON result cannot be matched back to handles except by position.
## Checking that a link's `marker=N` is the marker the report quotes (review-browser_dbg-paused-overlay.js)

- Question: is marker index 68457 in a report's link the `thread.resumed` marker it quotes?
- Command: `profiler-cli marker info m-104 --session …` prints type, time and fields but no marker index; `thread markers --list` does not show it either. Had to run `marker info m-98 m-102 … --json` and a python one-liner over `markerIndex` for the 12 links.
- Could have shown: the marker index in `marker info` text output (e.g. `Marker m-104 (index 68457)`), or a `thread markers --index 68457` lookup.
- Also: `profiler-cli --session X thread markers …` (option before the subcommand) fails with `unknown option '--session' (Did you mean --version?)`; placing `--session` after the subcommand works.
