## Which ports each failing run's servers got (harness stdout lines)

- Question: "which UDP/TCP port numbers did the test's http3server and node proxy get, in each of the 5 TIMEOUT jobs, and in the reruns that passed".
- The per-test profile has only the test's own `info()` lines; the `node moz-http2 [stdout] HTTP3 server listening on ports ...` lines, and the lines of a passing rerun, are only in the job log.
- Command I needed instead: download `live_backing.log` of each task from `fx-tests test netwerk/test/unit/test_http3_proxy.js --task-ids --issue 6`, then `grep -a "TEST-START | .../test_http3_proxy\|HTTP3 server listening"`.
- What could have shown it: `fx-tests task <id> --log-grep <re>` (or `--log-window <test>`: the job-log lines between a test's TEST-START and its end, including harness server output), so one command per task answers it.
