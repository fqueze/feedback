## Question: which assertion step fails in each failing job (the second message names it)
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_originControls.js` (Issues section), then a shell loop of `COLUMNS=400 fx-tests task <id> --messages --full-messages` over 19 task IDs.
- Expected: Issues groups by first message only ("Expected attention badge before clicking. - Got false, expected true", 57x). The message that follows it in the same run (`Correct l10n message. - {"extensionTitle":"Test extension ext4@test"}...`) says which step/extension failed. A way to break an issue down by the next message, or to list all messages per failing run, would have answered this in one command.
- Got: only the first message per run; needed 19 `fx-tests task` calls.
- Workaround: the loop above.

## Ambiguous "Passed when the harness reran it"
- Command: `fx-tests task Kbo1Tk54TsGoKFcNlhupGA --profiles` vs `fx-tests task TbC-llGITe6N54OX5pKjiw --profiles`
- Expected: both say "FAIL — 1 failing execution of 2"; one adds "Passed when the harness reran it", the other adds nothing. With 1 failure out of 2 executions, the other one presumably passed, so it is unclear what the missing line means.
- Workaround: ignored it.
