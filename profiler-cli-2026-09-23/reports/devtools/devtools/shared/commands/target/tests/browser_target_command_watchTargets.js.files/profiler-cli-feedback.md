## Daemon died silently between two commands (2026-09-22)

- Command: `PROFILER_CLI_SESSION_OWNER=browser_target_command_watchTargets.js python3 profile-link.py --session browser_target_command_watchTargets.js-1 --marker m-582` (which runs `profiler-cli marker info m-582 --session ... --json`), right after a series of `thread markers`, `marker info` and `marker stack` calls on the same session had worked.
- Expected: the marker's JSON.
- Got: `Session browser_target_command_watchTargets.js-1 is not reachable. Nothing is accepting connections on ...sock. The daemon exited without cleaning up.` The session's `.json` was gone too. The daemon log ends with `Received message: command` at 03:32:05 and no error. The machine had 16 GB free, and there was no OOM entry in the kernel log.
- Workaround: reloaded the profile under the same session name. The marker handles changed (m-582 became m-7), so every handle noted before had to be looked up again. It did not happen again on the reload or on the two other profiles.
- What would have helped: the daemon logging why it exited (signal, uncaught exception), and `load` warning when it replaces a session whose handles someone may still hold.

## Loading a raw Taskcluster URL selects a WebExtensions thread (review, 2026-09-22)

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/FlC3wzuUSmu5KmiZFAqGSw/runs/0/artifacts/public/test_info/profile_browser_target_command_watchTargets.js.json" --session review-watchTargets-4`, then `thread markers --search TEST-UNEXPECTED --list`.
- Expected: the parent process main thread selected, as it is when the same profile is loaded from a profiler.firefox.com link with `thread=0`, so the test log is found.
- Got: `t-110 (GeckoMain, WebExtensions)` selected, and "No markers match" with no hint that another thread has them.
- Workaround: `thread select t-0`.
- What would have helped: default to the parent process main thread, or have a zero-hit `thread markers --search` name the threads that do match.
