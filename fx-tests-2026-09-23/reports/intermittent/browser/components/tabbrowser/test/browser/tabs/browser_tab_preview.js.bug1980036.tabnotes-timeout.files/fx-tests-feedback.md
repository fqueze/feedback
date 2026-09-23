## Question: what actually failed in the runs whose "first failure" is a todo

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_preview.js` (Issues block), then `--task-ids --limit 0 --issue 4`
- Expected: issue 4 to be a real failure, or the expected-fail `todo()` line (`TEST-FAIL | ... tabNotesTests - Skip when tab notes is not enabled; see bug2008033`, a known fail present in every run where the pref is off) to be skipped when picking a run's first failure.
- Got: `70x FAIL Skip when tab notes is not enabled; see bug2008033` as a failure mode. Behind it, from the 68 logs: 33 x `tabNotesNewBadgeDismissedByPreviewPanelTests - TypeError: can't access property "click", addNoteButton is null`, 29 x `tabNotesNewBadgeVisibilityTests - TypeError: ... badge is null`, 5 x `panelSuppressionWhenOtherPanelsOpeningDuringDelayTests - panelOpener execute called - timed out`, 1 x a timeout. Three real failure modes hidden under a todo.
- Workaround: downloaded the 68 `live_backing.log`s and took the first `TEST-FAIL`/`TEST-UNEXPECTED-*` line that is not the todo.
- Could have shown: the first unexpected result (status != expected) instead of the first line with a FAIL status.

## Question: since when has this one failure mode stopped (per-issue history)

- Command: `fx-tests test <path> --history --issue 3`
- Expected: per-day counts for one failure mode, to date a step change in it while the test's other modes keep failing.
- Got: `--issue ... needs --task-ids`; `--history` is for the whole test, where this mode (15 of 270 timeouts) is invisible under the other subtests' timeouts.
- Workaround: task IDs per issue, downloaded logs, grepped the subtest and the log timestamp per job. (`--config linux` on `--history` did show the step, because Linux has few other failures.)
- Could have shown: `--history` honouring `--issue`, and the subtest name next to each task ID.

