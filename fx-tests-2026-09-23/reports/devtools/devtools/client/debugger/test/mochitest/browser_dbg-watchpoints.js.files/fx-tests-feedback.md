## Question: were these passes clean, or expected failures under `fail-if`?

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-watchpoints.js --history --config a11y`
- Expected: runs whose manifest expected a failure (`fail-if = ["a11y_checks"]`, 2026-09-11 to 2026-09-16) to be counted apart from clean passes, or the `fail-if` to be shown under Issues the way `skip-if` conditions are.
- Got: "278 pass (100.00%)", with nothing marking the ~180 runs that failed as expected.
- Workaround: `git log` on the manifest showed the `fail-if` being added and removed.
