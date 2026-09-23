## Sessions directory not writable in the sandbox: the error is good, but the brief does not say it

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session browser_tab_groups.js.timeout-1`
- Expected: the profile loads (the brief says nothing about a session directory).
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (profile-link.py reads it too). One wasted round trip; the hint itself was fine.

## Question: "in what order, to the millisecond, did these events happen?"

- Command: `profiler-cli thread markers --search "TabGrouped,TabUngrouped,TabGroupCreate,waitForEvent,Test" --list --limit 0`, even after `zoom push 271.0,271.2` (a 200 ms view).
- Expected: start times precise enough to order events a fraction of a millisecond apart (e.g. `t=271.0246`).
- Got: every row says `t=4m31s`. In a 5-minute profile the list's time column is rounded to the second, zoomed or not, so the list cannot say which of two events came first, or how long after one another.
- Workaround: `--json` and jq over `.flatMarkers[].start`. The text list could scale its precision to the zoomed range (or always show ms in a `--list`).

## A 113 MB profile: 60 s load timeout, then the daemon dies silently

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/FR9lvC3cQAWVF1sKIGsepg/runs/0/artifacts/public/test_info/profile_browser_tab_groups.js.json --session browser_tab_groups.js.timeout-1` (112,767,680 bytes gzipped).
- Expected: the profile loads like the other 18 profiles of 97–125 MB from the same test did, or a clear error.
- Got, first: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`. The others of the same size loaded close to that limit, so the default is tight for CI per-test profiles.
- Got, with `PROFILER_CLI_LOAD_TIMEOUT_MS=600000` (twice): after ~108 s the client prints only `Loading profile from ...` and returns; no error message. The daemon log ends at `Fetching profile from ...` with nothing after it, the socket and metadata are gone, and the next command says `Unknown session`. So the daemon died (out of memory?) mid-load, and neither the client nor the log says why.
- Workaround: none; this profile was not read (19 of the 20 timeouts were). The same profile also failed to load for the agent that wrote `browser_tab_groups.js.md`.
