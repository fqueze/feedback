# profiler-cli feedback (test_bug570341.html)

## Question: "at which profile time did this network phase (responseStart) happen?"

- Command: `profiler-cli marker info m-724 --session test_bug570341.html-1` on a Network marker.
- Expected: the phase fields (`responseStart`, `requestStart`, `fetchStart`, ...) in the same time base as the marker's own `Time`/`start`, so they can be compared with other markers (`Navigation::Start`, `DOMEvent`).
- Got: the fields are printed as "raw payload, no schema", in a base 7.293 ms ahead of every marker `start` (the `Uptime: 7.293ms` of `profile meta`), in both the parent and the content process. Nothing says so; `startTime: 152812.81` next to `Time: 2m33s` gives no hint that they differ.
- Workaround: `marker info --json`, subtract the payload `startTime` from the marker `start` to get the offset, apply it to every phase field by hand.
- What the output could have shown: the network phases converted to the profile's time base (as the profiler's network tooltip does), or at least relative to the marker's start.

## Session directory not writable in a sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session test_bug570341.html-1`
- Expected: a loaded session.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the error message did suggest `PROFILER_CLI_SESSION_DIR`, which worked.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call.
