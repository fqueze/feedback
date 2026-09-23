## Default session dir not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=test_nsIProcess.js profiler-cli load '<taskcluster profile URL>' --session test_nsIProcess.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, followed by a second error `Unknown session test_nsIProcess.js-1` from the next command.
- Workaround: prefix every command with `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` (the error message suggests it). Cost one round trip; the brief does not mention it.

## Question: how busy was the machine while test X ran?

- Command: `profiler-cli zoom push 611.0,614.5 --session <s>; profiler-cli thread markers --session <s> --search 'name:CPU Use' --list --limit 0` on a resource-usage profile.
- Expected: each `CPU Use` row shows its CPU percent (and idle %), so the list answers "was the machine saturated during this test".
- Got: rows show only name, time (rounded to whole seconds, `t=10m12s`) and duration; the percentages need `marker info` one by one, and the rounded time cannot place a 100 ms sample against a 700 ms test.
- Workaround: `--json` piped through a Python script printing `data.cpuPercent`, `data.idle_pct` and start times in ms, interleaved with the `test` markers.
- What would have answered it: the CPU percent as the `CPU Use` row label, and millisecond timestamps in `--list` when the zoom is a few seconds wide.

## Question: absolute times of a resource-usage marker

- Command: `profiler-cli thread markers --search test_nsIProcess --list` on a 29 min resource-usage profile.
- Got: `t=10m12s` for tests lasting under a second, so the offset to a per-test profile's start (needed to find the CPU sample at the failure) could not be computed from the default output.
- Workaround: `marker info m-2 --json` for `start`/`end` in ms.

## (review) `thread samples` keeps the last zoom after `zoom pop` / `zoom clear`

- Command: `profiler-cli zoom push 0.655,0.695 --session S; profiler-cli zoom clear --session S; profiler-cli thread samples --include-idle --session S` on the BEgx per-test profile (18 samples, per `thread info`).
- Expected: the full profile's 18 samples; the header and `status` both say "View: Full profile".
- Got: "0 running samples", `thread functions` "No functions in this thread". After `zoom push 0.35,0.47; zoom pop`, it reports 2 samples: the count of the last pushed range, not the full profile. Markers are not affected.
- Workaround: none within the session; reloaded the profile. Cost a raw-JSON download before I suspected the tool.

## (review) Question: when were this thread's samples taken, and where are the gaps?

- Command: none answers it; `thread samples` / `samples-top-down` aggregate, and their `--json` has no per-sample times.
- Wanted: the sample times (and CPU delta) in a range, to say "no sample from 623 ms to the end of the profile" rather than "none in this 24 ms window".
- Workaround: downloaded the artifact and summed `samples.timeDeltas` in Python.
- What would have answered it: a `thread samples --list` (time, CPU delta, leaf frame per sample), or a sample-gap summary in `thread info`.
