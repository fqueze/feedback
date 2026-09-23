## Question: which test's failures are sheriffs actually starring on this bug?

- Command: `fx-tests intermittent --bug 1767905`
- Expected: the "Tests named" and "Failure messages" sections to name the test in the starred lines of all 4 occurrences, `toolkit/mozapps/update/tests/unit_aus_update/languagePackUpdates.js`. Every occurrence's `lines` (in `--json`) is `TEST-UNEXPECTED-FAIL | toolkit/mozapps/update/tests/unit_aus_update/languagePackUpdates.js | profile uploaded in profile_languagePackUpdates-2.js.json`.
- Got: "Tests named, per annotated job: 1x security/manager/ssl/tests/unit/test_faulty_server.js", and failure messages `testMlkem768x25519NoX25519Fallback …` and `xpcshell return code: 0`. Both come from one job's secondary lines; the test behind all 4 jobs is missing. With a bug whose summary names another test (AWSY here), this reads as "misc xpcshell noise" instead of "sheriffs star languagePackUpdates.js on the wrong bug".
- Suspect: lines ending in `profile uploaded in …` are dropped as not being failure messages, and their test path goes with them.
- Workaround: read `occurrenceRows[].lines` in `--json`, and use Treeherder's `failuresbybug` API with `tree=all` over a longer window.
- Could have shown: a per-occurrence test path, and a warning when the tests in the starred lines don't match the test the bug summary names.
