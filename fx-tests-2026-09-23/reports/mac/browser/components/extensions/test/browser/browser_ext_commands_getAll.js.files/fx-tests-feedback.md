## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_getAll.js --bugs`
- Expected: a line saying no bug names the test (or the bugs found).
- Got: the usual test summary and nothing about bugs. From the text output alone I couldn't tell "no bug" from "the flag was ignored". `--json` showed `annotatedBugs: []`.
- Workaround: `--json` and read `annotatedBugs`.

## Question: "does every job where this test fails also have test Z failing with message Y?"

- The question: for a victim test, whether the suspected leaker failed with its own message in every one of the victim's failing jobs, and in which jobs the leaker failed without the victim failing.
- Commands: `fx-tests test <victim> --task-ids --limit 0` and `fx-tests test <leaker> --task-ids --limit 0`, diffed with `comm`. Then a shell loop of `fx-tests task <id> --messages | rg -A6 <leaker>` over the victim's 44 jobs.
- What could have shown it: a `--with <other test path>` (or `--co-failures`) on `fx-tests test`. It would list, per failing job, the other tests that failed before this one in the same job, with their first message. It would also count jobs where the other test failed and this one did not, by config. That would have shown "44/44 with the leaker's `No anchor node` rejection; the leaker's other 7 failing jobs are all -standalone" directly.
