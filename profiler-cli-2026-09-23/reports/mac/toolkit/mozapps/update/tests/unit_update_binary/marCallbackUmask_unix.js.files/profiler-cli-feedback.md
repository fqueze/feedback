## Daemon exits silently while loading a 51 MB per-test profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=marCallbackUmask_unix.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/V4kMUGJWRkqkja8roL0CpQ/runs/0/artifacts/public/test_info/profile_marCallbackUmask_unix.js.json" --session marCallbackUmask_unix.js-1` (3 GB reported free by available-gb.sh)
- Expected: the profile loads, or an error saying why not (size limit, out of memory).
- Got: the `load` command returned after "Loading profile from ...", and the next command said `Session marCallbackUmask_unix.js-1 is not reachable ... The daemon exited without cleaning up.` The daemon log ends at `Fetching profile from <url>` then status polls, with no error line. The artifact is 51.7 MB gzipped. Most failing runs of this test have 50–62 MB profiles (a 30 s xpcshell wait spinning the event loop), so most of them cannot be read.
- Workaround: picked the failing tasks with the smallest profiles (12–30 MB), found by HEAD requests on each artifact for `x-goog-stored-content-length`. `fx-tests task --profiles` could print each profile's size.

## Question: "was the machine saturated during this interval?" (resource-usage profile)

- Command: `profiler-cli zoom push 600,640 --session S; profiler-cli thread markers --search "CPU Use" --list --limit 0 --session S`
- Expected: something that answers the question at a glance, for example the CPU Percent of each `CPU Use` marker in the list row, or a per-second summary.
- Got: one row per 100 ms marker with only name, time and duration. The CPU percentage is only in `marker info`, one marker at a time.
- Workaround: `--json` and a Python script averaging `data.cpuPercent` per second.

## Question: "which tests were running during this interval?" (resource-usage profile)

- Command: `profiler-cli thread markers --category Tasks --list --limit 0 --session S` inside a zoom
- Expected: start and end times precise enough to tell whether a test overlapped a 2 s window.
- Got: past one minute, start times print rounded to the second (`t=22m48s`), and no end time is shown, so overlap with a window of a few seconds cannot be read off.
- Workaround: `--json` and a script printing `start` and `start + duration` in seconds.
