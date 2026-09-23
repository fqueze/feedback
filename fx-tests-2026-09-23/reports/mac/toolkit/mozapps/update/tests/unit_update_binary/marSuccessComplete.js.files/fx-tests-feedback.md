## Question: "in how many of this test's failing runs did the helper process print X?"

- Question: of the 58 failing runs of marSuccessComplete.js, how many show the updater's `launchchild_osx: Failed to run application ... errAETimeout` line in the replayed test output?
- Command tried: `fx-tests errors --harness xpcshell --message "Failed to run application"` — "No markers matched": the errors file only holds typed markers (C++ warning, JS error, console.*), not the replayed plain stdout of a child process.
- Workaround: downloaded the 58 jobs' `live_backing.log` (442 MB) and grepped them. Answer: 4 of 58 jobs, and in one of them the line names this test's copy.
- What could have answered it: `fx-tests test <path> --grep "<text>"` over the failing runs' replayed output, counting runs that contain it, or plain `output` lines in the errors file for failing xpcshell tests.

## Question: "how large is each failing run's per-test profile?"

- `fx-tests test --task-ids` / `fx-tests task --profiles` print profile URLs but not sizes. Half of this test's failing runs have 55–62 MB gzipped profiles that `profiler-cli` cannot load, so picking a readable one took 58 HEAD requests for `x-goog-stored-content-length`.
