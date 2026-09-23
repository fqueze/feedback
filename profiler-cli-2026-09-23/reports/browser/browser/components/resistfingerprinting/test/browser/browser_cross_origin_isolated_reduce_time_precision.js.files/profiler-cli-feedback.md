## Default load timeout too short for CI per-test profiles
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/QecISsE4SM6sG-5a2WKusw/runs/0/artifacts/public/test_info/profile_browser_cross_origin_isolated_reduce_time_precision.js.json" --session browser-rtp-1`
- Expected: the load to finish (or wait until it does).
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, then `Error: Profile still loading, try again shortly` on the next command.
- Workaround: poll `profile info` in an until-loop; set `PROFILER_CLI_LOAD_TIMEOUT_MS=300000` for later loads.

## Question: which of two markers on two threads, 0.1 ms apart, came first?
- Command: `profiler-cli marker info m-309 m-245 m-292 --session browser-rtp-1` (and `thread markers --list`)
- Expected: start/end times precise enough to order markers across threads.
- Got: `Time: 1m15s - 1m15s` / `t=1m15s` for every marker in the interesting second; had to use `marker info --json` and read `start`.
- Could have shown: millisecond (or finer) start times in `marker info`, or in `--list` when zoomed into a sub-second range.

## Question: was the machine busy over this time range? (resource-usage profile)
- Command: `profiler-cli thread markers --search "name:CPU Use" --session browser-rtp-4` on the resource-usage profile of task A3KvrR_zSpy4EisTXSXUFA, zoomed to the silent 6 minutes.
- Expected: some summary of the CPU Percent field over the range.
- Got: only the marker count and durations; `counter list` says "No counters in this profile". Had to script over `--list --json` to average `cpuPercent` per minute.
- Could have shown: min/avg/max of numeric marker fields (or a per-bucket average) in the `thread markers` summary, or expose CPU Use as a counter.

## (review) Daemon died silently mid-session
- Command: `profiler-cli thread select t-266 --session browser-review-xoi-rtp-2` on the EpFGpxS2T9iRrVA5IzZDvg per-test profile, a few queries after the load.
- Expected: an answer, or an error saying why the daemon exited.
- Got: `Session browser-review-xoi-rtp-2 is not reachable ... The daemon exited without cleaning up`. The daemon log ends on `Received message: command` with no error, and the kernel log shows no OOM kill.
- Workaround: reloaded the profile under a new session id (another 2+ minutes).

## (review) Retrying a timed-out load is refused
- Command: `profiler-cli load <url> --session browser-review-xoi-rtp-2` again, after the first attempt hit `Profile load timeout after 60000ms`.
- Expected: the retry to attach to the load still in progress, or to wait for it.
- Got: `Session browser-review-xoi-rtp-2 is already running. Stop it first`, then `Profile still loading` from `status`.
- Workaround: polled `status` in a background until-loop.

## (review) Question: which thread does a report link's `thread=Fu` open?
- Command: none exists. `profile-link.py` refuses a session loaded from a profiler link, which is how the review brief says to load one, and `thread list` does not show the URL thread parameter.
- Expected: some way to map the `thread=` value of another link on the same profile to a `t-N` handle.
- Got: had to decode the profiler's base-32 thread encoding by hand (`Fu` = 9*32+30 = t-318).
- Could have shown: the URL thread token as a column in `thread list`, or `thread select` accepting that token.
