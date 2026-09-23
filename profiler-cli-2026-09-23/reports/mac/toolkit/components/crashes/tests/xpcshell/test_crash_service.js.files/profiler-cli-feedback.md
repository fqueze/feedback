# profiler-cli feedback — test_crash_service.js

## Daemon dies silently loading a large (880 MB uncompressed) profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=test_crash_service.js profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/DCitXhdASda_LW2C3Aaojg/runs/0/artifacts/public/test_info/profile_test_crash_service.js.json --session test_crash_service.js-1`
- Expected: the profile loads, or an error saying why it cannot (e.g. "profile is 880 MB uncompressed, above the JS string limit of 512 MB").
- Got: `load` prints "Loading profile from ..." and returns; the next command says "Session ... is not reachable. The daemon exited without cleaning up." The daemon log ends at "Fetching profile from ..." with no error. Happened twice. The artifact is 51 MB gzip, 880 MB decoded (checked with curl), which is above V8's maximum string length (~512 MB), so a crash while decoding is the likely cause.
- Workaround: picked other failing tasks whose profile is smaller (debug builds, 13-15 MB gzip). All the opt macOS profiles of this test are 37-58 MB gzip and probably hit the same wall, so the worst config cannot be read at all.
- What would help: stream-parse, or at least catch the failure and print it (and the size) in the `load` output and the daemon log.

Later: the 37 MB gzip opt profile of b5zd3nLIRHuABMKguuJ6jw did load, so the limit is between 37 and 51 MB gzip for these profiles. Most of their size is `DummyEvent` Runnable/AddTask marker pairs (7.4 M markers in 30 s).

## (review) When was this range's one sample taken, and how many samples does a zoomed range hold?

- Question: a 42.9 ms microtask checkpoint held one sample; was it before or after a marker inside it (the analyzer launch)? And, for a zoomed range, how many samples is "100%"?
- Command: `profiler-cli zoom push 1.5825,1.6252 --session S; profiler-cli thread samples-top-down --include-idle --session S`
- Expected: the sample count of the view in the text output, and some way to list the samples of a small range with their timestamps and stacks.
- Got: percentages only. The count is only in `thread samples --json` (`categoryBreakdown.totalSamples`), and there is no per-sample listing, so I bisected with `zoom push` over 10 ms slices and a `--json` count per slice to place the sample at 1.605-1.615 s.
- Workaround: the bisection above, 5 commands per sample.
- What would help: print "N samples" in the header of `thread samples*` when zoomed, and a `thread samples --list` (time + leaf/stack) for small ranges.
