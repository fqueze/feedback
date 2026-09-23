## `test --bugs` is silent when no bug is found

- Command: `fx-tests test dom/serviceworkers/test/browser_pbm_cleanup.js --bugs`
- Expected: a line such as "No sheriff-annotated bug names this test".
- Got: the same output as without `--bugs`; only `--json` showed `"annotatedBugs": []`.
- Workaround: `--json` and a Bugzilla quicksearch to confirm there is none.

## Which execution of a test failed, when a job runs it from two manifests

- Question: "which manifest's run of this test failed in this job, and which passed?" The test
  runs from `browser-dFPI.toml` and `browser.toml` in the same job; `fx-tests task TM0v1lQzRCeX0ACCSXH-jA`
  says "FAIL — 1 failing execution of 3" without saying which manifest, or what time, each
  execution ran at.
- What would have answered it: per execution, the manifest and its start time, next to the
  outcome. That is what made the passing run in the same job a control for the failing one.
- Workaround: downloaded `live_backing.log` and scripted a per-manifest summary.

## Shutdown-leak failures: "No failing test named a per-test profile" hides where the evidence is

- Command: `fx-tests task XT2nnNDkT6CzVDIOU9ervA --profiles`
- Got: no profile listed for the test, which reads as "no profile evidence".
- The resource-usage profile had it: the `FAIL` marker for "leaked window until shutdown" carries
  the ShutdownLeakPathFinder retention path as its stack. A hint for leak failures pointing at
  that marker would have saved reading the raw log first.
