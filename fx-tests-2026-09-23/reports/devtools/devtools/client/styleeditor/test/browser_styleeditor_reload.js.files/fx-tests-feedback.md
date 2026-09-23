## Question: which per-test profiles contain a *passing* run of test X?

- Need: a passing run of `browser_styleeditor_reload.js` to compare with its failing runs. No per-test profile exists for a passing run, but the per-test profile of a *later* test in the same manifest and job covers the whole browser session, including the passing run.
- Commands: `fx-tests test <each later test in the manifest> --task-ids --limit 0`, then a shell `comm` against `fx-tests test <X> --task-ids --limit 0` to keep jobs where X did not fail.
- Expected: something like `fx-tests test <X> --passing-profiles` listing per-test profile URLs (of other tests, later in the same manifest run) whose session includes a PASS of X.
- Got: nothing direct; took 6 `fx-tests test` calls plus a script. Workaround worked (found ShBwFJTMQ-27mkmkcLBcfQ via browser_styleeditor_telemetry.js).
