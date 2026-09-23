## `--bugs` prints nothing when it finds no bug

- Command: `fx-tests test devtools/client/application/test/browser/browser_application_panel_telemetry-start-worker.js --bugs`
- Expected: a "Bugs" section, saying "none" when no sheriff-annotated bug names the test.
- Got: the same output as without `--bugs` (19 lines, nothing on stderr, exit 0). Cannot tell "no bug" from "the flag did nothing".
- Workaround: Bugzilla REST `short_desc=telemetry-start-worker`, which found bug 1789734 (single tracking bug, RESOLVED INCOMPLETE, last changed 2026-09-21).

## Crashes leave only the resource-usage profile, and `task --profiles` does not say where the crash's log is

- Command: `fx-tests task HpBYmzfVRa6IS0LkXojHSw --profiles`
- Got: "No failing test named a per-test profile in this job." for every one of the 7 crashing tasks tried.
- Could have shown: that for a CRASH the resource-usage profile holds the test's full log (INFO lines, C++ warnings with pid/thread, the assertion text and the stack dump), so that is the profile to read.

## Question: a passing job of this test on the failing config, to compare its resource-usage profile

- Needed: the task ID of a job on test-macosx1500-aarch64/debug-mochitest-devtools-chrome where this test passed (and one on linux2404-64 debug).
- Commands tried: `fx-tests test <path> --task-ids` (failing tasks only), `--coverage`, `--executions` (counts only).
- Workaround: Treeherder `/api/project/mozilla-central/push/?revision=...` and `/api/jobs/?push_id=...&count=2000&offset=...` for neighbouring pushes, then `fx-tests task <id> --json` on every chunk and a script over `.passed[]` to find the chunk that ran the test. About 15 requests.
- Could have shown: `fx-tests test <path> --passing-task-ids --config <c>` (or a few passing task IDs per config under `--coverage`).
