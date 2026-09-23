# profiler-cli feedback (marFailurePartialZucchini.js)

## Per-test profile load: daemon dies silently

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marFailurePartialZucchini.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/BBEl2ULuQjasGTHfyP53Qg/runs/0/artifacts/public/test_info/profile_marFailurePartialZucchini.js.json" --session marFailurePartialZucchini.js-3`
  (same with task COszRY6cQ3eYMfxkW91UAg, session -4)
- Expected: the profile loads, or an error saying why not.
- Got: `Error: Session marFailurePartialZucchini.js-3 is not reachable. Nothing is accepting connections on ...sock. The daemon exited without cleaning up.` The daemon log's last line is `Fetching profile from ...`; no error recorded. The artifact is 56 MB gzipped, 878 MB uncompressed (the resource-usage profile's `artifact` marker says `profile_marFailurePartialZucchini.js.json — 878MB`), so likely over V8's max string length or the heap limit while parsing. 7 GB free on the machine at the time.
- Workaround: none within the brief (must load the Taskcluster URL). Used the test log replayed in the job's resource-usage profile instead.
- Would help: a clear error in the CLI and daemon log ("profile too large to parse: N MB"), or streaming JSON parsing.

## Per-second machine CPU over a zoom range

- Question: was the machine saturated while the test was waiting?
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` piped to a script averaging `data.cpuPercent` per second.
- The default output could have shown: a per-bucket CPU summary for the current zoom (like `counter info`'s "over time" section) for resource-usage profiles, whose CPU is in `CPU Use` markers rather than a counter.
