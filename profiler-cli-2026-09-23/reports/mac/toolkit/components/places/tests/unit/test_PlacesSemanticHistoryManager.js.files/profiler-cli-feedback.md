# profiler-cli feedback

## Question: what was the machine's CPU use while one test ran?

- Command: `profiler-cli thread markers --search 'name:CPU Use' --list --limit 0` after `zoom push <test marker>` on a resource-usage profile.
- Expected: a summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (mean, min, share of time at 100%), or a time-bucketed view like `counter info` gives for counters.
- Got: one row per 100 ms marker (406 rows for a 40 s test), with no CPU value in the row text; the percentage is only in `marker info` or `--json`.
- Workaround: `--json` and a Python script bucketing `data.cpuPercent` by 5 s.

## (review) `thread samples` keeps the last zoom's range after `zoom pop` / `zoom clear`

- Command (profiler-cli 0.9.0, X5J per-test profile): `zoom push 25.760,30.333`, `thread samples --include-idle` (80 samples, correct), then `zoom clear`, `thread samples --include-idle`.
- Expected: the whole profile's 546 samples, since the header and `status` both say `View: Full profile`.
- Got: the same 80 samples. After `zoom push 1.3,14.6` then `zoom clear`, it gave that range's 242. After `zoom push 20,21` then `zoom pop`, 16. Markers and `profile info` do follow the pop.
- Workaround: `zoom push 0,<end>` instead of clearing. Silent: a sample count taken after a pop looks like a whole-profile figure.

## (review) default session directory not writable in the agent sandbox

- Command: `profiler-cli load <url> --session review-test_PlacesSemanticHistoryManager.js-1`, with `PROFILER_CLI_SESSION_DIR` unset (also with the sandbox disabled).
- Got: `EPERM mkdir ~/.profiler-cli`. The error suggests `PROFILER_CLI_SESSION_DIR=$TMPDIR/profiler-cli` and warns that the socket path must stay short, so the session name was shortened to `rv-psh-1` rather than risk it (the long name was not tried).
- Expected: the default session directory to fall back to `$TMPDIR` when home is not writable.
