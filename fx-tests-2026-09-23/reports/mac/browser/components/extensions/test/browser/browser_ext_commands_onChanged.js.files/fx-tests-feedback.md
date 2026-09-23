# fx-tests feedback — browser_ext_commands_onChanged.js

## Question: did the failure come back after the fix, or were old revisions rerun?

- Command: `fx-tests test <path> --history` and `--task-ids`.
- Expected: some way to tell that the 3 failures dated 2026-09-14 are jobs on autoland revisions pushed on 2026-08-14/15, before the 2026-09-04 fix.
- Got: `--history` buckets by run day, so a fixed failure looks like it recurred 10 days after the fix. `fx-tests task <id>` prints the revision but not its push date.
- Workaround: `curl https://hg.mozilla.org/integration/autoland/json-rev/<rev>` for each revision, to read `pushdate`.
- What would have answered it: the push date next to the revision in `task` and `--task-ids`, or a history view keyed by push date. Flagging runs on revisions older than the window's step change would also do it.

## `--bugs` with no match prints nothing about bugs

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_onChanged.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: output identical to the run without `--bugs`, so I could not tell "no bug found" from "flag ignored".
