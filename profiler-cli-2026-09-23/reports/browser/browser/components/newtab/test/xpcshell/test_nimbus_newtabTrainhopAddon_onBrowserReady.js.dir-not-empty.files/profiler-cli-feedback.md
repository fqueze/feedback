## A failed `load` leaves its session id taken

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Uny-icA1ShC8XEj50L2y3w/runs/0/artifacts/public/test_info/profile_test_nimbus_newtabTrainhopAddon_onBrowserReady.js.json" --session browser-onBrowserReady-dne-3`
- Expected: the load fails (404) and the session id stays free.
- Got: `Error: Profile load failed: Could not fetch the profile on remote server. Response was: 404 Not Found.`, then the next `load --session browser-onBrowserReady-dne-3` with a valid URL failed with `Error: Session browser-onBrowserReady-dne-3 is already running. Stop it first or choose a different session id.`
- Workaround: `profiler-cli stop browser-onBrowserReady-dne-3`, then load again.

## (review) A session name built from the report name overflows the socket path

- Command: `profiler-cli load '<profile URL>' --session browser-review-test_nimbus_newtabTrainhopAddon_onBrowserReady.js.dir-not-empty-1`
- Expected: the session starts; the brief asks for `review-<report name>` in session names.
- Got: `Error: The Unix socket path for this session is 113 bytes, over this platform's 107-byte limit`, after the profile URL had been parsed.
- Workaround: a shorter session name (`browser-review-onBrowserReady-dne-1`). Checking the length before starting, or hashing long ids into the socket name, would avoid the retry.

## (review) Question: in what order did these instant markers happen, to the microsecond?

- Command: `profiler-cli thread markers --session <s> --search 'TaskQueue,PutEvent,ResolveOrReject,...' --list --limit 0`
- The list prints `t=3.391s` for five markers that are 40 to 200 us apart (dispatch, PutEvent, result, next dispatch), so it cannot say whether a `PutEvent` follows a `TaskQueue::DispatchLocked` or which result precedes which dispatch.
- Workaround: `--json` and a script printing `start` to 10 us. A `--precision us` option, or printing times relative to the view start with enough digits in a zoomed view shorter than 100 ms, would have answered it.
