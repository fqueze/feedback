## Daemon dies silently while loading a large per-test profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli PROFILER_CLI_SESSION_OWNER=test_webtransport_simple.js profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/eCf60qkVSlacNouLiKr-MA/runs/0/artifacts/public/test_info/profile_test_webtransport_simple.js.json --session test_webtransport_simple.js-3` (tried twice; one other session loaded, 8 GB available per available-gb.sh)
- Expected: the profile loads, or an error saying why (out of memory, too many markers).
- Got: `load` prints "Loading profile from ..." and returns; the next command says "Session ... is not reachable ... The daemon exited without cleaning up." The daemon log stops at "Fetching profile from ..." with no error line. The artifact is 60 MB stored (the two profiles of the same test that loaded were 30 s long with ~4.3M markers, mostly `DummyEvent` Runnable + `TaskController::AddTask` from the macOS xpcshell idle loop).
- Workaround: none; skipped this profile and used the two that loaded.

## Default session directory is not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=test_webtransport_simple.js profiler-cli load <url> --session test_webtransport_simple.js-1`
- Expected: loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a hint to set PROFILER_CLI_SESSION_DIR. The hint is good; the brief and `profile-link.py` do not mention it, so every command then needs the extra variable.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (profile-link.py honours it).
