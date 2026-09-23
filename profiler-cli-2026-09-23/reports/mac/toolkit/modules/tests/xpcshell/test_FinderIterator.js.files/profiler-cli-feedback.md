## Question: how far apart are consecutive markers of one kind (timer runnables every ~150 us)?

- Command: `profiler-cli thread markers --search 'anonymous,Perform microtasks' --list --limit 0 --session <id>` inside a 270 ms zoom.
- Expected: start times precise enough to subtract (sub-ms), or a delta-from-previous column.
- Got: `t=1.228s`, `t=1.229s` ... — millisecond resolution once t >= 1 s, so 40 runnables 150 us apart print as runs of identical times; the cycle length cannot be read.
- Workaround: `--json` and a Python script over `flatMarkers[].start`.
- What would have answered it: finer time formatting when the zoom is short (or always 3 decimals of ms), or a `--delta` column in `--list`.

## Setup: default session dir not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session <id>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the error message itself suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked (and `profile-link.py` honours it).
- Cost: one round trip; the brief does not mention the variable, so every command needs it prefixed as well.


## (review) Same millisecond-resolution problem, hit again

- Command: `profiler-cli thread markers --list --limit 0` in a 20 ms zoom at t≈1.23 s (and at t≈0.83 s the 2-decimal ms output still hid 1-10 µs gaps).
- Workaround again: `--json` and a script over `flatMarkers[].start`. Cost: one extra dump and script per profile.
