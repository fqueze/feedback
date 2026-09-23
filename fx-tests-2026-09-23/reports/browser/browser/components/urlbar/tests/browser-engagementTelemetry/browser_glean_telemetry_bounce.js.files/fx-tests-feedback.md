## `fx-tests test <path> --bugs` prints nothing when no bug is found
- Command: `fx-tests test browser/components/urlbar/tests/browser-engagementTelemetry/browser_glean_telemetry_bounce.js --bugs`
- Expected: a "Bugs: none annotated in the window" line, and possibly the closed bugs whose summary names the test (bug 1980746, closed INCOMPLETE, under the test's old path `engagementTelemetry/browser/`).
- Got: exactly the output without `--bugs`. The JSON has `"annotatedBugs": []`, so I could not tell "none found" from "the flag was ignored" without switching to `--json`.
- Workaround: `--json | jq .annotatedBugs`, then a Bugzilla REST search on the summary.
