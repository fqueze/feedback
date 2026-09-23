## `fx-tests test --bugs` prints nothing when no bug is annotated

- Command: `fx-tests test accessible/tests/browser/events/browser_test_focus_urlbar.js --bugs`
- Expected: a line saying no sheriff-annotated bug names the test in the window (and ideally the open bugs whose summary names it: here bug 2040774 and bug 1883471, both NEW).
- Got: the normal report with no bug section at all; `--json` has `annotatedBugs: []`. Silence reads like the flag was ignored.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<file name>`.

## Question: how often does the harness retry of a failing test also fail?

- Needed to tell an order dependency from an independent race. Found it in `fx-tests test <path> --executions` ("278 failing runs across 278 jobs; 0 of those jobs saw the failure more than once") only after sampling 40 jobs with `fx-tests task` in a loop.
- The default `fx-tests test` output could show that line next to the verdict, since "never twice in one job" is what the diagnosis brief keys on.
