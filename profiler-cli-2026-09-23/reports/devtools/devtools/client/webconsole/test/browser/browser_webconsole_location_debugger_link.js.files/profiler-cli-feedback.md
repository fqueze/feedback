## Reviewer (review-browser_webconsole_location_debugger_link.js)

### How late did each timer fire / what is the gap between successive markers matching X
- Command: `profiler-cli thread markers --search "setTimeout() for tryOnce" --list --limit 0 --session <s>`
- Expected: a way to see the interval between successive matching markers (or a timer's due time vs run time), to spot late timers.
- Got: absolute start times only; I parsed `t=…s` out of the text list with a Python one-liner to get the gaps. The frequency analysis in the non-`--list` output gives min/avg/max interval, but not per-occurrence gaps.
- Workaround: grep + python over the `--list` output.

### Checking a link's `marker=N` needs `--json` and a parser
- Command: `profiler-cli marker info m-3 --session <s>`
- Expected: the marker's index (the `marker=N` in profiler links) in the default output.
- Got: no index; had to use `--json` and a python snippet per marker to read `markerIndex`.
- Workaround: `profiler-cli marker info <m> --json | python3 -c '…d["markerIndex"]…'`.

### `--session` before the subcommand is rejected
- Command: `profiler-cli --session review-bwldl-1 zoom push 5.40,5.90`
- Expected: a global option accepted anywhere.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.
