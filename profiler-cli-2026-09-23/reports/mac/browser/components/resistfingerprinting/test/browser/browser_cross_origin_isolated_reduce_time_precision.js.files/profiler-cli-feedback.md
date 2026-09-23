## Daemon dies silently while loading a Taskcluster profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=browser_cross_origin_isolated_reduce_time_precision.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/EpFGpxS2T9iRrVA5IzZDvg/runs/0/artifacts/public/test_info/profile_browser_cross_origin_isolated_reduce_time_precision.js.json" --session browser_cross_origin_isolated_reduce_time_precision.js-2` (twice, ~2 minutes apart; one other session of mine loaded).
- Expected: the profile loads (113 MB gzipped; a 123 MB one loaded fine just before), or an error saying why not.
- Got: `Error: Session ... is not reachable. Nothing is accepting connections on ....sock. The daemon exited without cleaning up.` The daemon log ends at `Fetching profile from ...` with no error line.
- Workaround: read another task's profile of the same failure instead.

## Question: in which order did markers within the same second happen?

- Command: `profiler-cli thread markers --session S --category Network --list --limit 0` (and the same on the content GeckoMain and DOM Worker threads).
- The list prints `t=1m15s` for every row, so the ordering of the worker's `DOMEvent message`, its `TaskController::AddTask`, and the main thread's `WrDisplayList` / `Image Paint` (all within 0.3 ms) could not be read. Needed `--json` and a script printing `start` in ms.
- Could have shown: millisecond (or finer) start times in `--list` rows, at least when the listed range is short or many rows share the same rounded second.

## Question: which processes and threads were alive during the stall?

- Command: `profiler-cli thread list --session S` / `profile info --all`.
- The thread list has no start/end times; `profile info --all` only prints them as `ts-` handles. Finding the content process (and its DOM Worker) that existed from 75.35 s to the end of a 335-thread profile needed `profile info --all --json` and a script filtering on `startTime`/`endTime`.
- Could have shown: `thread list --alive-at <t>` or `--alive-during <range>`, or process start/end in seconds.

## Session-path-too-long error suggests the directory already in use

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <taskcluster URL> --session review-browser_cross_origin_isolated_reduce_time_precision.js-1`
- Expected: either a load, or a fix that works (a shorter `--session` name).
- Got: `The Unix socket path for this session is 109 bytes, over this platform's 103-byte limit ... Use a shorter session directory, for example: PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` — the very directory already set. The session name was the long part.
- Workaround: a shorter session name (`review-coirtp-1`). The error could say how many bytes the name vs. the directory take, and suggest shortening `--session` too.

## `thread info` says a thread is still alive after its process ended

- Command: `profiler-cli thread info --thread t-323 --session review-coirtp-1` (QecISsE4SM6sG-5a2WKusw per-test profile).
- Expected: an end time matching its process, which `profile info --all --json` gives as `endTime` 75185.4 ms.
- Got: `Ended at: still alive at end of recording`.
- Workaround: `profile info --all --json` and a script over the processes' `startTime`/`endTime`.

## Question: in each content process, which thread created a given service first?

- Command: `profiler-cli profile markers --search be65e2b7 --session review-coirtp-1`
- Its `Matches by thread` section stops at 10 threads with no note that it was cut, so "which of the 9 worker-running content processes had the `GetService` marker on the DOM Worker rather than GeckoMain" needed `--limit 0 --json` plus `profile info --all --json` and a script joining thread handles to thread names.
- Could have shown: the thread name (GeckoMain / DOM Worker) next to the process in `Matches by thread`, and a "N more threads" line when it is truncated.
