## `thread markers --list` times rounded to whole seconds

- Command: `profiler-cli thread markers --category Test --search browser_devices_get_user_media_queue_request --list --limit 0 --session gum_queue_request-1` (profile 1m7s long)
- Expected: marker start times with ms resolution, so the order of events within one second can be read (the whole test ran within ~3s).
- Got: every row `t=1m4s` / `t=1m5s`; nothing distinguishes 54 ms apart.
- Workaround: `--json` and a script printing `flatMarkers[].start` in ms.
- Question it should answer: "in what order, and how far apart, did these markers happen".

## Default session dir not writable in the sandbox

- Command: `profiler-cli load <taskcluster url> --session ...`
- Got: EPERM on `~/.profiler-cli`; the error message did say to set `PROFILER_CLI_SESSION_DIR`, which worked. Just one wasted round trip; noting it since the brief does not mention it.

## (review) Which marker does a link's `marker=N` point at

- Question: "which marker does this link select", to check a report's links against what it quotes.
- Command: `profiler-cli load '<profiler.firefox.com link with marker=562788>' --session ...`. Load prints the thread and range, but not the selected marker. Then `thread markers --search <quoted text> --list`, then `marker info <m-…> m-… --json`, reading `markerIndex` one candidate at a time. That took about 25 links across four profiles and a dozen round trips.
- What could show it: `load` printing the handle of the marker the URL selects, or `marker info` accepting a marker index (for example `marker info --index 562788`). Also, a `markerIndex` column in `thread markers --list --json` would let one list answer every link on a thread.
