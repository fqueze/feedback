## A `todo()` (TEST-KNOWN-FAIL) is reported as the failure

- Command: `fx-tests test accessible/tests/browser/hittest/browser_test_general.js` (and `fx-tests task <id> --messages`)
- Expected: the first TEST-UNEXPECTED-FAIL of each run, here `Timeout while waiting for cache update` / `Wrong direct child accessible at the point (11, 119) of [DOM node id: container ...] sought [DOM node id: aNode ...]`.
- Got: `Issues (first failure per run): 12x FAIL Bug 746974 - children must match on all platforms...`, which is the test's `todo(false, ...)` at the top of `runTests`, logged as TEST-KNOWN-FAIL in every run, passing ones included. `task --messages` lists it first among the failure messages too.
- Workaround: read the test's `Test` markers in the per-test profile (`thread markers --category Test --list`), where it is `TEST-KNOWN-FAIL`, to find the real failure.
