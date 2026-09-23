## Question: was the machine's disk saturated during this test / over the job? (resource-usage profile)

- Command: `profiler-cli thread markers --search "CPU Use" --list --limit 0 --json` and `--search name:IO ...`, then a Python script to average `iowait_pct` and sum `write_bytes` per 5 s window and over the job.
- Expected: `counter list` or `profile info` on a resource-usage profile summarising CPU %, IO wait % and disk write throughput over time (these profiles have no counters, only per-100ms markers).
- Got: "No counters in this profile."; the per-marker values are there but no aggregate. The answer (IO wait 30-45% and a flat ~12 MB/s write ceiling on all 16 failing jobs, vs 2-3% and bursts to 125 MB/s on passing ones) needed the script.

## Question: when does a thread have no samples at all?

- Command: repeated `zoom push a,b` + `thread samples-bottom-up --include-idle` to bisect where the parent process had zero samples (54-59 s, 60-63 s: parent main thread in an uninterruptible fsync, so the sampler stalled).
- Expected: a sample-gap listing (e.g. in `thread info` or `thread samples --include-idle`: "no samples from X to Y").
- Workaround: manual bisection, about 25 calls.

## Minor: long session name

- Command: `profiler-cli load <url> --session browser-browser_translations_select_ai_feature.js-ru-REhyQLimQEO71FN_RoChkQ >/dev/null`
- Got: load refused (socket path too long; the message is clear on stdout), then the next command said `Unknown session ...: no metadata found`, which does not hint at the cause.
- Expected: a non-zero exit from `load`, and/or the later error mentioning the failed load.

## (review) Global `--session` placement

- Command: `profiler-cli --session <id> thread markers ...`
- Expected: `--session` accepted before the subcommand, as a global option (the brief says to pass it on every call).
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.

## (review) Question: how many samples does this thread have between A and B?

- Command: `profiler-cli zoom push A,B` then `thread info`, which printed "This thread contains 4660 samples" for the whole thread, not for the zoom. I then ran `thread samples-bottom-up --include-idle --json` and read `invertedCallTree.totalSamples`, about 20 times to find the parent-process sampler gap (53.46–62.5 s).
- Expected: `thread info` to give the sample count in the current view, and ideally the gaps in sampling ("no samples from X to Y").
