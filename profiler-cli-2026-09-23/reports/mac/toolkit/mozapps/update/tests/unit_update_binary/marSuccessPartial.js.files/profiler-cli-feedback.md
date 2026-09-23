## `load` of a 57 MB per-test profile: daemon dies silently, `load` exits 0

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marSuccessPartial.js profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/fHKIwkcoRSGJjDLlotw6yg/runs/0/artifacts/public/test_info/profile_marSuccessPartial.js.json --session marSuccessPartial.js-1` (57 MB gzipped; 4-7 GB free)
- Expected: the profile loads, or `load` exits non-zero saying why (out of memory, too large).
- Got: three attempts. The first printed nothing past "Loading profile from ..."; the next command said the session was not reachable. The third ran 77 s and exited 0, again with no session. The daemon log ends at "Fetching profile from ...", with no error. Every opt-build failure profile of this test is over 50 MB, so no opt config can be read from its per-test profile.
- Workaround: HEAD requests for `x-goog-stored-content-length` on all 49 artifacts, then loading the debug-build ones under 35 MB. `fx-tests task --profiles` printing each profile's size would have saved that step.
- Also: the default session directory `~/.profiler-cli` cannot be created in this sandbox (EPERM). The error does suggest `PROFILER_CLI_SESSION_DIR`, which worked.

## Question: "was the machine saturated while this test waited?" (resource-usage profile)

- Command: `profiler-cli zoom push 612,653 --session marSuccessPartial.js-3` then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json --session marSuccessPartial.js-3`, piped to Python averaging `data.cpuPercent` per second. `cpuPercent` is a string (`"100.0%"`), so the first script failed on `sum()`.
- Expected: a per-second (or per-bucket) CPU summary for the zoom, as `counter info` gives for counters.
- Got: one row per 100 ms `CPU Use` marker, without the percentage.
- Could have shown: `profile info` or `thread markers` summarising `CPU Use` markers as a CPU track over the zoom, with the numeric value.

## Question: "which tests overlapped this one?" (resource-usage profile)

- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json --session marSuccessPartial.js-3`, then Python keeping markers with `start < end_self && start + duration > start_self`.
- Expected: a way to list the markers that overlap a given marker or interval, with start and end to 0.1 s.
- Got: `--list` start times past one minute are rounded to the second (`t=10m19s`), and no end time is shown.
