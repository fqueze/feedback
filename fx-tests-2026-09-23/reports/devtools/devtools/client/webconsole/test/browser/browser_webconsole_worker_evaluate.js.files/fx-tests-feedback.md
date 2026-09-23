## `fx-tests test <path> --bugs` prints nothing when no bug is annotated

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_webconsole_worker_evaluate.js --bugs`
- Expected: a line such as `Annotated bugs: none` so the reader knows the lookup ran.
- Got: the same output as without `--bugs`, no Bugs section at all; only `--json` showed `"annotatedBugs": []`.
- Workaround: `--json` to confirm the empty list, then a Bugzilla REST search on the test name.
- Question the default output did not answer: "does any bug name this test?"

