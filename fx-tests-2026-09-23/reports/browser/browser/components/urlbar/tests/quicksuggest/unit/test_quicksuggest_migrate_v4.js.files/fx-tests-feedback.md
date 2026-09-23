## `test --bugs` prints nothing when no bug names the test

- Command: `COLUMNS=250 fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_migrate_v4.js --bugs`
- Expected: a line such as "No sheriff-annotated bug names this test" after the Issues block.
- Got: the normal output with no bugs section at all, identical to running without `--bugs`, so I could not tell "none" from "the flag did nothing". `--json` showed `annotatedBugs: []`.
- Workaround: `--json` and read `annotatedBugs`.

## Question: "are all the crashes in this job the same hang?"

- Command: `fx-tests task OhIqoTLOS8mvD3DHPhOAMg --limit 0`, then grep, then a loop of `profiler-cli marker stack` over the 138 CRASH markers of the resource-usage profile.
- The job had 138 tests crashing with `child process hang at shutdown`. Whether my test is one of many victims of one job-wide problem was the first question, and answering it took a grep over the text output (per-directory counts) and a scripted loop over stacks.
- What the output could have shown: in `task`, the failures grouped by signature with a count ("138 CRASH child process hang at shutdown, across 23 directories") ahead of the per-test list; and for a crash signature that is an override (like this one), the process type and the first non-system frame of each dump, grouped (here: 102 GPU process in nsAppShell::Init, 35 GPU process in RenderThread::ShutDown).

## `failures --message` does not find a crash signature

- Command: `fx-tests failures --message "child process hang at shutdown"`
- Expected: the tests behind that message, or a pointer to `fx-tests crashes`, since it is the top xpcshell crash signature (19,349 crashes, 503 tests).
- Got: "No failure matched ... Check --path, --message and --component for typos."
- Workaround: `fx-tests crashes`.
