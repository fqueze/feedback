## Question: which runs failed with one message hidden under a todo "first failure"

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_preview.js --task-ids --limit 0 --issue 4 --full-messages`
- Expected: a way to list the tasks behind `tabNotesNewBadgeVisibilityTests - ... badge is null`, the failure I was asked about.
- Got: it is not an issue row; it sits under issue 4 `Skip when tab notes is not enabled; see bug2008033`, a `todo()` known-fail that precedes it in every run. Issue 4's 70 tasks mix three failure modes. `fx-tests test` has no `--message` filter.
- Workaround: grepped the 68 `live_backing.log`s a sibling agent had already downloaded (`browser_tab_preview.js.bug1980036.tabnotes-timeout.files/logs4`) for `badge is null`: 29 tasks.
- Could have shown: the first unexpected result rather than the first FAIL-status line (a todo is expected), or a `--message <text>` filter on `--task-ids`. Same root issue as the sibling's feedback.
