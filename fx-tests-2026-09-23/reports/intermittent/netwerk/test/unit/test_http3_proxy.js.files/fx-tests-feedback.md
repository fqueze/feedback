## The harness-side log lines that explain an xpcshell failure (node server stderr)

- Question: "what did the node test server print when this failure happened" (here `Error: listen EADDRINUSE: address already in use :::<port>` and the `*Code.startServer` frame, one per occurrence).
- The failure message `child process exit closing code: 1 signal: null` is only the node parent's summary; the reason is in `node moz-http2 [stderr]` lines of the job log, which neither the per-test profile nor `fx-tests task`/`fx-tests test` shows.
- Command I needed instead: a loop of `curl .../live_backing.log | grep -a "EADDRINUSE: address\|Code\.startServer"` over the 17 task ids of `fx-tests test netwerk/test/unit/test_http3_proxy.js --task-ids --issue 4`.
- What could have shown it: `fx-tests task <id> --messages` (or `--log-context`) including the `node moz-http2 [stderr]` / `mozserve` lines emitted between the test's TEST-START and its failure; or `fx-tests test <path> --issue N --log-grep <re>`.

## `--task-ids --limit 0` without `--issue` appends a second, out-of-order date list

- Command: `COLUMNS=250 fx-tests test netwerk/test/unit/test_http3_proxy.js --task-ids --limit 0`
- Expected: one chronological list of the 29 failing jobs, or one list per issue.
- Got: the chronological list 2026-09-01..09-19, then a second block starting again at 2026-09-04 (the TIMEOUT jobs), with no header saying it is a different issue. It reads like a sorting bug.
- Workaround: run once per `--issue N`.
