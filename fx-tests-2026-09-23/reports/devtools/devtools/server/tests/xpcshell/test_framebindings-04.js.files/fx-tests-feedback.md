## Which jobs of a config were mass-TIMEOUT jobs

- Question: on test-windows11-64-25h2/opt-xpcshell-msix, which jobs had the whole-job breakage (hundreds of tests TIMEOUT), and did every one of them contain a given test?
- Commands: `fx-tests test <other test> --config <msix> --task-ids --limit 0`, then `fx-tests task <id>` in a loop over 12 task IDs to read each `Outcomes` line.
- Could have shown: in `test --task-ids`, the job's TIMEOUT count next to each task ID (e.g. `JOcVul… 1060 TIMEOUT of 1472`), so a failure that is one of a thousand in its job stands out without opening each job.

## `task` shows a different message than the job logged

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles --limit 0`
- Got: `test_framebindings-04.js` TIMEOUT with message `Test timed out`, while the resource-usage profile's replayed log for it holds `… | Timed out and was force-killed by the harness …` (same in FADM/LKys, where `task` does show that one). Which message `task` picks looks arbitrary between the TIMEOUT test_end message and the buffered ERROR.
