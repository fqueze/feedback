## `--bugs` prints nothing when no bug is annotated
- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_telemetry.js --bugs`
- Expected: a line such as "Annotated bugs: none" after the Issues block.
- Got: the same output as without `--bugs`; only `--json` showed `annotatedBugs: []`, so I could not tell "no bug" from "flag ignored / query failed".
- Workaround: `--bugs --json | jq .annotatedBugs`, then a Bugzilla REST summary search (which found the RESOLVED tracking bug 2024015).
