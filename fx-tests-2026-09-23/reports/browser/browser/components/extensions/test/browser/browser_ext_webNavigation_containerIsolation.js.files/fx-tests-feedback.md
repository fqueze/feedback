## `--bugs` prints nothing when no bug is annotated

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webNavigation_containerIsolation.js --bugs`
- Expected: a line such as "Annotated bugs: none" after the Issues section.
- Got: the exact same output as without `--bugs`; no bugs section at all. Only `--json` showed `"annotatedBugs": []`.
- Workaround: `--json | jq .annotatedBugs` to tell "no bug" from "flag ignored".

## Question: "which test in this job failed first?" (the leaker of a cascade)

- Command: `fx-tests task <taskId> --messages --limit 0` (and `--json`, `.failures[]`), on 31 jobs where ~45 tests each fail the end-of-test vsync check.
- Expected: failures in execution order, or a start time per failure, so the first one in the cascade is visible.
- Got: the order is not execution order (e.g. Xh6MnwM2R3OFkWAeuPuZLA.0 lists browser_ext_incognito_views.js before browser_ext_commands_execute_page_action.js, which ran earlier) and the JSON has no timestamps. Had to load the resource-usage profile to get the order.
- What would have answered it: execution order, or a `startTime` per failure in `--json`, or a "first failure in this browser session" hint for runs of identical messages.
