## Question: did this test fail before the window started?

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marFailurePartial.js --history`
- Question: the history is flat from the first day of the window (2026-08-31), so it cannot say whether the failure started with a landing a few days earlier (bug 2036593, on central 2026-08-27).
- What I did: listed mozilla-central pushes for 2026-08-15..08-31 with the Treeherder API, picked the `test-macosx1500-aarch64/debug-xpcshell` jobs, downloaded 12 `live_backing.log`s and counted `TEST-TIMEOUT | .../unit_update_binary/` in each (0 in 6 jobs before, 8 in 4 of 5 jobs after).
- What the output could have shown: when the window starts with failures on day one, say so ("failing since before the window") and offer a pre-window sample, even a small one: per-job counts from a handful of older jobs of the failing configs.

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marFailurePartial.js --bugs`
- Expected: a line such as "No sheriff-annotated bug names this test".
- Got: the same output as without `--bugs`; only `--json` showed `annotatedBugs: []`, so I could not tell "none" from "the lookup silently failed" without a script.
