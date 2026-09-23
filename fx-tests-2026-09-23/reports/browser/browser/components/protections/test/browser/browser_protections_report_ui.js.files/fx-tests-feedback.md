## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/protections/test/browser/browser_protections_report_ui.js --bugs`
- Expected: a line such as "Annotated bugs: none" after the Issues section.
- Got: the same output as without `--bugs`, with no bugs section at all, so "no bugs" looks the same as "flag ignored" or "lookup failed silently".
- Workaround: `--json` and reading `annotatedBugs` (which was `[]`), then a Bugzilla REST summary search to find bugs naming the test (bug 1775776, bug 2058214 exist but are not sheriff-annotated in the window).

## `fx-tests task <id>` without a run number blames artifact expiry when run `.0` simply is not the failing run

- Command: `fx-tests task bW6VZwTdR9mYHd8xLJby_w --profiles` (reviewer, 2026-09-22)
- Expected: the failing run (`.1`, the one the report's links use), or a list of the task's runs.
- Got: "has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent" — for a task from this month, whose run `.1` has all its artifacts.
- Workaround: `fx-tests task bW6VZwTdR9mYHd8xLJby_w.1 --profiles`.
