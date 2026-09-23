## `--bugs` misses the test's own tracking bug

- Command: `fx-tests test devtools/shared/test-helpers/browser_allocation_tracker.js --bugs`
- Expected: bug 1767786, "Intermittent devtools/shared/test-helpers/browser_allocation_tracker.js | single tracking bug" (REOPENED), which names the test by its full path.
- Got: the usual report with no bug section and no "no bugs found" line either, so I could not tell "none" from "not looked up".
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=browser_allocation_tracker.js"`.

## Question: "where does this test fail most, including beta/esr?"

- `fx-tests test <path>` covers trunk only (10 failures / 912 runs). `fx-tests intermittent --bug 1767786` (default) said "no sheriff annotations ... on trunk", and only `--since 21 --tree all` showed 28 of the 31 annotations are on mozilla-beta (linux2404-64-devedition), where the test also fails its retry: the busiest failure mode was not visible from `test`.
- What would have shown it: `fx-tests test` noting that the test's bug has annotations on other trees (with counts), or a `--tree` option on `test`.
