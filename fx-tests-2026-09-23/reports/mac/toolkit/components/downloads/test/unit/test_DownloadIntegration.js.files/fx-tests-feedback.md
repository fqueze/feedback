## Question: in the jobs where this test failed, which other tests failed too?

- Commands: `fx-tests test <path> --task-ids --limit 0` for test_DownloadIntegration.js, then the same for test_DownloadCore.js, test_DownloadLegacy.js and test_DownloadList.js (450 and 515 task rows for the last two), and a shell join on the task IDs.
- Expected: an option on `fx-tests test <path>` such as `--co-failures`, listing the tests that also failed in the same jobs, with how many of this test's failing jobs each appears in. The answer here was that all three failed in all 20 macOS jobs where this test failed, which points at a per-job condition rather than at this test.
- Got: the task IDs only; `fx-tests task <id>` answers it for one job at a time (20 calls).
- Workaround: the four `--task-ids` outputs and a `rg -c` join in a shell loop.
