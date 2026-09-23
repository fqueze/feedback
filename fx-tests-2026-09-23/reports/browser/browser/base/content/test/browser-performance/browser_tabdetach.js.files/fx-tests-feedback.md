## `fx-tests test` reports a `todo()` (TEST-KNOWN-FAIL) message as the failure

- Command: `fx-tests test browser/base/content/test/performance/browser_tabdetach.js` (also `--full-messages`)
- Expected: under "Issues (first failure per run)", the message that made the run fail. In all 13 runs that is
  `uncaught rejection: Unexpected undefined tabState for onMoveToNewWindow aFromBrowser`.
- Got: `13x FAIL known reflow at clientPos@chrome://browser/content/tabbrowser/drag-and-drop.js was encountered 2 times`.
  That message comes from `todo(false, ...)` in the performance head.js `reportUnexpectedReflows`: it is logged on
  every run, passing ones included, and is not a failure. `fx-tests task <id> --messages` lists it next to the real
  one with no status, so one cannot tell which is the failure without reading head.js.
- Workaround: `fx-tests task <id> --messages` for each task, then reading the test helper to find that the first two
  are `todo`s.
  Likely origin: in the job's resource-usage profile, the `todo()` is a TestStatus marker named `FAIL` with no
  `Expected` field (m-25 in task Kfx86gSNRqSpHwv2ePb9RA's profile_resource-usage.json: `FAIL — known reflow at
  clientPos@... was encountered 2 times`). A status of FAIL with an expected of FAIL is TEST-KNOWN-FAIL, not a failure;
  the per-test profile labels the same entry `TEST-KNOWN-FAIL`.
