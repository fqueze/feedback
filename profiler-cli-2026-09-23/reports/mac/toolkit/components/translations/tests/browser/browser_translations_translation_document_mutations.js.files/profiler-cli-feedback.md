## Marker list timestamps too coarse to order markers inside a zoom

- Command: `profiler-cli zoom push 184.27,184.8 --session tdm-1; profiler-cli thread markers --session tdm-1 --list --limit 0 --search "-name:IPCOut,..."`
- Expected: timestamps precise enough to order markers within the 530 ms view (the question was "did the refresh tick come before or after the test's third `tryOnce` runnable, ~0.3 ms apart?").
- Got: every row printed as `t=3m4s` (whole seconds), even inside a 530 ms zoom, so the chronological order was only implied by row order and the gaps were invisible.
- Workaround: `--json` and a Python script printing `start/1000` with 4 decimals. The list output could print times relative to the zoom start, or with ms precision when the view is under a few seconds.

## Question: "delay from each occurrence of marker A to the next marker B"

- Question: for each "Creating the TranslationsDocument." INFO marker, how long until the next `RefreshDriverTick` whose reason includes "Update intersection observations", and was that tick a `RefreshDriver::EnsureTimerStarted::catch-up` or a vsync one?
- Command: `profiler-cli thread markers --session tdm-2 --list --limit 0 --search "Creating the TranslationsDocument,RefreshDriverTick,catch-up,..." --json` piped into a script.
- What could have shown it: a "next marker matching X after each marker matching Y" / pairing mode on `thread markers`, printing the gap per occurrence.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <taskcluster url> --session ...`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'` (the error message did suggest `PROFILER_CLI_SESSION_DIR`, which worked).
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. Falling back to `$TMPDIR` automatically would have saved a round trip.

## Review: `--session` rejected before the subcommand

- Command: `profiler-cli --session review-mutations-1 zoom push 184.260,184.400`
- Expected: `--session` accepted as a global option, as `load --session` suggests.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand. Saying "pass --session after the subcommand" in the error would have saved the round trip.

## Review: same question as "Marker list timestamps too coarse" above

- Question: in a 140 ms zoom, did the first intersection-observation tick come before or after the three `tryOnce` polls, which are 0.1 ms apart?
- Command: `profiler-cli thread markers --list --limit 0 --session review-mutations-1` (every row `t=3m4s`), then `--json | jq '.flatMarkers[] | .start/1000'`.
