## Which crash signature did each failing task have?

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_tab_switching.js --task-ids --limit 0`
- Expected: next to each CRASH task, its signature (the Issues list has 14x `@ mozalloc_handle_oom` and 7 distinct `@ xul.dll + 0x...`), so the unsymbolicated ones can be picked for a closer look.
- Got: task ID, job name and minidump ID only; `--json` `taskIds[]` has no `signature` field either.
- Workaround: ran `fx-tests crash <task> <minidump>` on 9 tasks one by one to find a `xul.dll` one (UOhyqd4cT6Sk0qxOnch74Q.0).
