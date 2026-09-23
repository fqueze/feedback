## `task --profiles` omits the shutdown-hang profile
- Command: `fx-tests task Oh4bXax-Rm6iUYo9SqfqqA --profiles`
- Expected: every profile the job uploaded for the failure, including `profile_shutdown_hang_2433.json`, which the job log names (`TEST-UNEXPECTED-FAIL | shutdown hang | profile uploaded in profile_shutdown_hang_2433.json`) right after the timed-out test, in the same browser.
- Got: only the three per-test profiles and the resource-usage profile; the shutdown hang is not listed as a failure of the job either.
- Workaround: downloaded `live_backing.log` and grepped for `profile uploaded in`. That profile showed the Renderer still blocked 5 minutes later, which is what made the hang permanent rather than slow.

## `test --bugs` prints nothing when no bug names the test
- Command: `fx-tests test devtools/client/inspector/test/browser_inspector_breadcrumbs.js --harness mochitest --bugs`
- Expected: a "Bugs: none" line.
- Got: the same output as without `--bugs`, so "no bug" and "flag ignored" look alike.
- Workaround: none needed; took it as no bug.
