## `fx-tests test --bugs` prints nothing when there is no annotated bug

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-sidebar-toggle.js --bugs`
- Expected: a line such as "Annotated bugs: none".
- Got: the same output as without `--bugs`; only `--json` shows `"annotatedBugs": []`.
- Workaround: `--json`.

## `fx-tests task` header lost the revision on a transient definition read

- Command: `fx-tests task XzvJ4VKtSuGqLmkt8QN8fA --profiles`
- Got: "warning: could not read the definition of task XzvJ4VKtSuGqLmkt8QN8fA, so the job name, repository and revision are missing from the header"; a `curl` of the same task definition a minute later worked.
- Workaround: `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>` for `GECKO_HEAD_REV`. A retry inside fx-tests would avoid it.
