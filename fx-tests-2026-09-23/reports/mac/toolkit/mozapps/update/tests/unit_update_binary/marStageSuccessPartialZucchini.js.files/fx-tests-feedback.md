## `--bugs` with no bug prints nothing at all

- Command: `COLUMNS=250 fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marStageSuccessPartialZucchini.js --bugs`
- Expected: an "Annotated bugs: none" line, so that "no bug" can be told apart from "the lookup did not run".
- Got: the normal output with no bugs section. I had to run `--json` to see `"annotatedBugs": []`.
- Workaround: `--json | python3 -c '...annotatedBugs...'`.

## Question: did this test fail before the data window started?

- The failures started with a landing (2026-08-26) that was 5 days before the window (2026-08-31 … 2026-09-20), so `--history` shows the rate as steady and cannot date the step.
- Command used instead: Treeherder `/api/project/autoland/push/` + `/jobs/` to list macOS 15 xpcshell jobs before and after the landing, then a grep of about 240 `live_backing.log` for `will retry`, `Callback log does not exist yet`, and whether `unit_update_binary/xpcshell_base.toml` was in the job's test paths (most autoland jobs do not run the manifest).
- What would have answered it: a `fx-tests test <path> --around <rev|date>` that samples jobs from before the window for this test's failure message, or at least says "the window starts after the first failure; the step is older than the data".
