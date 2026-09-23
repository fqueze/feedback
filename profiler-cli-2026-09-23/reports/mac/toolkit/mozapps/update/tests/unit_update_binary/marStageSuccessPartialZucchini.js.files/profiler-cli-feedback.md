## Daemon exits silently while loading a large per-test profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marStageSuccessPartialZucchini.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/e9gzvdqFRUelHlgHWxb0Xg/runs/0/artifacts/public/test_info/profile_marStageSuccessPartialZucchini.js.json" --session marStageSuccessPartialZucchini.js-3` (twice, with 3 GB then 6 GB reported free by available-gb.sh)
- Expected: the profile loads, or an error saying why not (size limit, out of memory).
- Got: `Error: Session marStageSuccessPartialZucchini.js-3 is not reachable. ... The daemon exited without cleaning up.` The daemon log ends at `Loading profile from <url>` followed by status polls, with no error line. The artifact is 57 MB gzipped (the 27 MB one from task V4kMUGJWRkqkja8roL0CpQ, with 4.9 M markers, loaded fine), so this is probably a JS string or heap limit on the decompressed JSON.
- Workaround: picked another failing task whose profile is smaller.
