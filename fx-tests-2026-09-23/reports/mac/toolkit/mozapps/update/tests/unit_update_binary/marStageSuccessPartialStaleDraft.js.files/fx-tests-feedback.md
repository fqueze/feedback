## `fx-tests test <path> --bugs` prints no bug section when there is none

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marStageSuccessPartialStaleDraft.js --bugs`
- Expected: a "Bugs: none found naming this test" line (or the list).
- Got: the same output as without `--bugs`, with no mention of bugs at all, so "no bug" and "the flag was ignored" look identical.
- Workaround: a Bugzilla quicksearch for the file name (`/rest/bug?quicksearch=marStageSuccessPartialStaleDraft` → `{"bugs":[]}`).

## Question: "in the jobs where this test failed, which other tests of the same directory failed too?"

- Commands: `fx-tests test <each of the 39 macOS unit_update_binary tests> --task-ids --limit 0`, then a script intersecting the task-ID sets with this test's 70.
- Result that answered it: in 26 of 70 failing jobs no other `unit_update_binary` test failed; in 25 one did, 12 two, 5 three, 2 four.
- What the output could have shown: `fx-tests test <path> --co-failures [--dir <dir>]`, listing for the failing jobs the other tests that failed in the same job, with counts. It separates "fails alone" from "fails as a batch", which is the first question for a parallel-only failure.

## Question: "did it fail before the window?"

- The window starts 2026-08-31; the suspected regressor landed 2026-08-26/27. Answering needed the Treeherder API (`/api/project/mozilla-central/push/`, then `/jobs/?push_id=…&offset=…`, paginated by 2000) and a `live_backing.log` scan of 32 jobs. An `fx-tests` command that scans job logs for a test's TEST-TIMEOUT over a date range outside the index window would have answered it.

