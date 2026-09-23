## Question: how far apart are two markers in a resource-usage profile?

- Command: `profiler-cli thread markers --session tvf-1 --search test_videocontrols_focus --list --limit 0`
- Expected: start times precise enough to order the test start, the "Waiting for video to load" INFO, and the IPC error, which are ~100 ms apart.
- Got: every row at `t=18m44s`; past one minute the list rounds to whole seconds.
- Workaround: the same command with `--json`, reading `flatMarkers[].start`.
- Could have shown: milliseconds in the `t=` column (e.g. `t=18m44.311s`), at least when neighbouring rows share the same rounded value.

## Default session directory not writable in the agent sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session ...`
- Expected: a session.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message itself gave the fix, which cost one round trip.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (profile-link.py honours it too).

## (review) Question: what do the markers matching a search say, and what are their marker indexes?

- Command: `profiler-cli thread markers --session review-tvf-1 --search PTextureConstructor,TimedTexture,name:CRASH --list --limit 0`
- Expected: each row with its `markerIndex` and its message or signature, so the quoted text and the link's `marker=N` can be checked in one call.
- Got: the name and handle only. Checking took `--json | jq -r '.flatMarkers[].handle' | xargs profiler-cli marker info --json | jq ...`.
- Could have shown: a `--fields message,signature` option or an index column on `--list`.
