## Question: what message did this failure actually print?

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_ReadHeapSnapshot_with_allocations.js`
- Expected: the Android FAIL's message, which the job's resource-usage profile has: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`.
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)`. Across the heapsnapshot sibling reports, this one label covers at least two different failures: the LMK kill, which really has no message, and this harness refusal, which has an INFO line.
- Workaround: loaded the resource-usage profile and searched for the test name. The Issues list could show the harness's `Failed to start process` INFO line for the test when there is one.

## Question: does any bug name this test?

- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs: none" line, or a list of bugs.
- Got: the same output as without `--bugs`, with no line about bugs at all. I could not tell "searched, found none" from "flag ignored".
- Workaround: Bugzilla REST quicksearch.
