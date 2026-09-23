## The failing tasks of one test on a try push

- Question: which tasks, and which profiles, are behind this test's failures on the try push?
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/shared/test/browser_treeWidget_keyboard_interaction.js --profiles --task-ids`
- Expected: the per-config table plus the task IDs and profile URLs of the failing runs (both flags were given).
- Got: only the per-config table; `--task-ids` and `--profiles` were silently ignored.
- Workaround: `fx-tests try <rev> --profiles --task-ids --limit 0 --full-messages` (the default view cut the perma-fail list to 10 of 35, and this test was not among the 10), then grep for the test.

## Whether a failing test has a per-test profile at all

- Question: does this failure have a per-test profile?
- Command: `fx-tests task c-PF-EUaRPSlBPuS0kPnZQ --profiles`
- Expected: a profile line for the test, or an explicit "no per-test profile was uploaded for this failure".
- Got: no profile line for browser_treeWidget_keyboard_interaction.js (other tests in the same list have one), and nothing saying it is missing; I had to list the task's artifacts to confirm none existed. In all 3 jobs no per-test profile was uploaded for this test and the harness did not retry it, so only the resource-usage profile covers it.
- Could have shown: "no per-test profile (not uploaded)" and "not retried by the harness" on the row.
