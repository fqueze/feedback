## Daemon dies during load with nothing in its log

- Command: `PROFILER_CLI_SESSION_OWNER=languagePackUpdates.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/V4kMUGJWRkqkja8roL0CpQ/runs/0/artifacts/public/test_info/profile_languagePackUpdates-2.js.json" --session languagePackUpdates.js-2` (with one other session of mine loaded, machine at ~2 GB available per `available-gb.sh`)
- Expected: the profile loads, or an error saying why it could not (out of memory, killed).
- Got: `Error: Session languagePackUpdates.js-2 is not reachable ... The daemon exited without cleaning up.` The daemon log ends with routine `status` messages and has no error line, so there is no way to tell an OOM kill from a crash.
- Workaround: stopped my other session and reloaded.
- Suggestion: have `load` report the daemon's exit signal or status, and have the daemon log its RSS when loading.

## A 62 MB (gzipped) per-test profile cannot be loaded at all

- Command: same `load` as above, retried with no other session of mine loaded and `available-gb.sh` at 6.
- Expected: the profile loads, or an error naming the limit it hit.
- Got: the same "daemon exited without cleaning up"; the daemon log stops at `Fetching profile from ...` with no error. The profile is 62 MB gzipped (the first run's, 17 MB, loaded fine); most of it is ~1.5 million `DummyEvent` Runnable markers per 35 s from a busy main thread.
- Workaround: read the retry's test output from the task log instead.
- Suggestion: say "out of memory while parsing (N MB)" instead of dying silently; a way to drop a marker name at load time (`--skip-markers Runnable,TaskController::AddTask`) would make such profiles loadable.

## Question: "how busy was the machine while this test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push m-2 --session languagePackUpdates.js-ru1` then `profiler-cli thread markers --search 'name:CPU Use' --list --limit 0 --json | python3 ...` to get the median/max of `data.cpuPercent` over the 1,381 `CPU Use` markers in the test's range.
- Expected: `counter list` / `counter info` to summarize machine CPU over the zoom, as the guide describes for counters.
- Got: `No counters in this profile.`; the resource-usage profile carries CPU only as `CPU Use` interval markers, and `thread markers` aggregates them by count and duration, not by their `cpuPercent` value.
- What would have answered it: a numeric summary (min/median/max) of a chosen marker field over the view, e.g. `thread markers --search 'name:CPU Use' --stat cpuPercent`, or exposing these markers as a counter.
