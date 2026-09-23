## `--bugs` prints nothing when it finds nothing, and missed a bug naming the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js --bugs`
- Expected: a Bugs section listing bug 2072654 ("Intermittent browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js | single tracking bug", filed 2026-09-16), or at least an explicit "no bugs found" line.
- Got: the normal output with no Bugs section and no message, so "no bug" and "lookup silently failed" look the same.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>`. (The bug was filed from an ESR153 job and has no trunk annotations, which may be why the sheriff-annotation path missed it.)

## Question: "did every failure happen on a revision before landing X?"

- Context: the main failure mode stopped when a fix landed on 2026-09-04 15:45 UTC, but 3 failures were dated 2026-09-14. They turned out to be backfills of 2026-08-15 pushes.
- Commands: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id>` for each task to get its revision, then hg `json-pushes?changeset=<rev>` for each push date. hg.mozilla.org then started answering 406 after about 15 calls, so a few stayed unverified.
- What would have answered it: the push date (or revision) next to each task ID in `--task-ids`, or a `--history` keyed by push date rather than run date. A run date is misleading for backfills and retriggers.
- Follow-up: I got the remaining push dates from Treeherder, `https://treeherder.mozilla.org/api/project/autoland/push/?revision=<rev>` (`push_timestamp`). That is data fx-tests already reaches, so it could print it.

## Review: Issues are not grouped when the message embeds an extension UUID (browser-review)

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js --task-ids --limit 0`
- Expected: one issue, `uncaught rejection: PageActions: No anchor node for _<uuid>_`, 46x, so `--issue <n>` lists the tasks of that failure mode.
- Got: one "1x" issue per run, since each message has its own UUID, so there is no single issue number for the 46 runs.
- Workaround: took a Windows task from the unfiltered `--task-ids` list and checked it with `fx-tests task <id>`. Normalizing UUIDs (and `_<uuid>_` widget ids) before grouping would fix it.
