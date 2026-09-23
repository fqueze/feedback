## `fx-tests test` Issues hides the only failure mode

- Command: `fx-tests test dom/base/test/test_bug333064.html`
- Expected: the first failure message of each run, "Test timed out." (40 of 40, checked in 9 job logs and 2 profiles).
- Got: `40x FAIL Failure details not recorded (likely Android or platform logging issue)`. All 40 runs are macOS 15 aarch64 desktop jobs, not Android.
- Workaround: `fx-tests task <id> --profiles` per job, then the profile's TestStatus markers.

## `fx-tests task` shows the fallout message, not the first failure

- Command: `fx-tests task RvTP9NRFTcyoxaOUB4101w --profiles`
- Expected: `Test timed out.` for test_bug333064.html, which is the first TEST-UNEXPECTED-FAIL of that test (profile marker m-342, Subtest "Test timed out.").
- Got: `[SimpleTest.finish()] No checks actually run. ...`, which is logged 1 s later by `killTest` -> `SimpleTest.finish()` as a consequence of the timeout. A reader takes this for a test with no assertions, not a 300 s hang.
- Likely cause: the timeout record has its text in `subtest` with an empty `message`, and the tool picks the first non-empty `message`.

## `fx-tests test --bugs` prints nothing about bugs

- Command: `fx-tests test dom/base/test/test_bug333064.html --bugs`
- Expected: a "Bugs" section, or an explicit "no open bug names this test".
- Got: the same output as without `--bugs`, no Bugs section at all, so "none found" and "flag ignored" look the same.

## Question: in which jobs did manifest B run right after manifest A, and did the test fail there?

- The question: this test failed only in jobs where `dom/base/test/fullscreen/mochitest.toml` ran immediately before `dom/base/test/mochitest-bug1.toml`. To say whether that is necessary and sufficient I needed the passing jobs too, with their manifest order.
- What I did: a script over the Treeherder push and jobs APIs plus each task's `public/test_info/manifests.list` (`adjacency.py` in this directory), about 2400 jobs.
- What could have shown it: `fx-tests test <path> --preceded-by <manifest>` or a per-run "previous manifest in the same job" column, split by config (the failure was hardware-opt only: 12/21 adjacent, 0/193 not adjacent; 0/13 debug and 0/11 VM adjacent).
