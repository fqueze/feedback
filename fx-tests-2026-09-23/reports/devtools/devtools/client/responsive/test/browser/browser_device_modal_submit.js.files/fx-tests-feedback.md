## Question: "did test X fail on this try push, and in which tasks?"
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids`
- Expected: the test (6 failures in 6 runs) listed, or a clear "N more tests (--limit 0)" line near the top.
- Got: the test silently absent from 722 lines of output; only `--limit 0` (1455 lines) showed it. The truncation marker was not obvious to find.
- Workaround: `--limit 0` and rg for the test name. A `--test <path>` filter that keeps the failure rows (messages, task IDs, profile URLs) for one test would answer it directly.

## `try --all-jobs --test <path> --task-ids --profiles` ignores --task-ids/--profiles
- Command: `fx-tests try 2888bcab0070 --all-jobs --test devtools/client/responsive/test/browser/browser_device_modal_submit.js --task-ids --profiles`
- Expected: task IDs and profile URLs for the failing config's jobs.
- Got: only the per-config ran/pass/fail table, no task IDs or profiles.
- Workaround: the full unfiltered `try --limit 0` listing.
