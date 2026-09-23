## `test --bugs` prints nothing when there is no annotated bug

- Command: `fx-tests test browser/components/originattributes/test/browser/browser_firstPartyIsolation_aboutPages.js --bugs`
- Expected: a "Bugs" section, saying "no sheriff-annotated bug names this test" when empty.
- Got: output identical to the run without `--bugs`; no section at all, so "no bug" looks the same as "flag ignored / query failed".
- Workaround: `--bugs --json` and checking `annotatedBugs == []`, then a Bugzilla REST summary search.

## Question: "a passing run of this test on this config, after date X, with its resource-usage profile"

- Needed to compare the failing runs with first-run passes (before and after the failures stopped), on test-macosx1500-aarch64/debug-mochitest-browser-chrome.
- No fx-tests command gives passing task IDs for a test/config. Scripted: Treeherder `api/project/mozilla-central/push/` -> `api/jobs/?push_id=` filtered on the job name -> Taskcluster task definition, grepping `payload.env.MOZHARNESS_TEST_PATHS` for the manifest (the chunk that runs a manifest changes from push to push).
- What could have shown it: `fx-tests test <path> --task-ids --passed [--config ...] [--day ...]`, listing a few passing task IDs with their profile URLs.
