## `load` of a large profile: daemon dies of V8 OOM, and `load` exits 0 without saying so

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marStageSuccessPartialStaleDraft.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/RYaZzv7pRq-Tdo2ShDkBEg/runs/0/artifacts/public/test_info/profile_marStageSuccessPartialStaleDraft.js.json" --session marStageSuccessPartialStaleDraft.js-1`
  (58 MB gzipped, 876 MB of JSON uncompressed)
- Expected: the profile loads, or `load` exits non-zero with "the daemon ran out of memory (JS heap limit 4 GB)".
- Got: the first attempt printed "Session ... is not reachable ... The daemon exited without cleaning up"; the next two printed only "Loading profile from ..." and **exited 0**, after which every command said the session was not reachable. The daemon log ends at "Fetching profile from ...", with no error: the daemon's stdio is `ignore`, so the reason is lost.
- Diagnosis: running the daemon's foreground command by hand (`node .../profiler-cli.js --daemon <url> --session <id>`) shows `FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory` at ~4 GB, during gunzip (`CompressionStream<ZlibContext>::AfterThreadPoolWork`). Also, the "foreground command" printed in the error message includes `--use-env-proxy`, which the installed Node (`~/.mozbuild/node`) rejects with `node: bad option: --use-env-proxy`, so it cannot be run as printed.
- Workaround: none within budget found yet; picked other tasks whose profiles are smaller (see below).
- Suggestions: redirect the daemon's stderr to its log; have `load` report the daemon's exit (the code already records `earlyExit`) instead of exiting 0; print the profile's size when it is large, and maybe raise `--max-old-space-size` or stream-parse.

## Question: "which tests were running at the same time as mine, in this job?" (resource-usage profile)

- Command: `profiler-cli zoom push 600,660 --session marStageSuccessPartialStaleDraft.js-2` then `profiler-cli thread markers --category Tasks --list --limit 0 --json`, and a Python script over `flatMarkers` keeping `test` markers whose interval overlaps the one of `m-1` (my test's `test` marker) and whose `Test Name` contains `unit_update_binary`, dropping SKIPs (0 s).
- Why a script: the zoom keeps markers that *start* in the range, the list interleaves ~300 `test` markers with SKIPs of 0 s, and `--search` cannot say "overlapping m-1" or "duration > 0". What its output could have shown: `thread markers --overlapping m-1 --search name:test` (or `--min-duration` applied to the `test` markers), with start–end and status per row.
- Also: `--search name:FAIL` matched every `test` marker whose test name contains "fail" (the `name` payload key caveat from the guide); `--search FAIL --category ...` did not help either, since `FAIL` and `test` markers share the payload key.

## Follow-up to the OOM entry above

- Workaround used: debug-build profiles of the same failure (26–34 MB gzipped) load fine; every opt and shippable profile of this test is 58–62 MB gzipped (millions of `DummyEvent` markers), so the worst config could only be read from its job log.
- Cost: four failed loads and a hand-run daemon before the cause was visible, about 10 minutes.

