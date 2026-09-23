# fx-tests feedback (browser_ext_commands_execute_page_action.js)

## Issues list does not normalize UUIDs, so one failure mode shows as 48

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js`
- Expected: one Issues row, `48x FAIL uncaught rejection: PageActions: No anchor node for _<uuid>_`.
- Got: `1x TIMEOUT` rows ranked first as "3x", then 48 separate `1x` rows, one per extension UUID
  (`_9fff699c-…_`, `_06985443-…_`, …), truncated at 10 with `… 40 more`. The most frequent
  failure mode looks like the least frequent, and the 3 timeouts look like the main one.
- Workaround: read the full `--limit 0` list and count by eye.

## Question: which failing runs predate a given landing?

- Question: "did any of these failures happen on a revision that contains the fix?"
- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id>` per task for
  the revision, then `curl hg.mozilla.org/.../raw-file/<rev>/<test>` per revision (51 tasks,
  took over 10 minutes).
- What the output could have shown: the revision (and its push date) next to each task ID. The
  runs are grouped by the day the job ran, so the three 2026-09-14 failures looked like a
  regression after the fix, when they were jobs run on 2026-08-14/15 revisions. `--history`
  has the same blind spot.

## `--task-ids` job name disagrees with `fx-tests task`

- Command: `fx-tests test <path> --task-ids --limit 0` lists
  `IT0S_eCQRJ-SkMnxZ3h6jA.0  test-macosx1500-aarch64/opt-mochitest-browser-chrome-1`
- `fx-tests task IT0S_eCQRJ-SkMnxZ3h6jA` says `test-macosx1500-aarch64/opt-mochitest-browser-chrome-7`.
- Expected: the same chunk in both.

## `--bugs` shows no bug for a test that has one

- Command: `fx-tests test <path> --bugs`
- Expected: bug 1378104 ("Intermittent browser/components/extensions/test/browser/test-oop-extensions/browser_ext_commands_execute_page_action.js | Test timed out"), which sheriffs annotated 18 times in the last 30 days.
- Got: the usual output with no bugs section and no "no bug found" line, so it is not clear
  whether the flag did anything. Possibly because the summary names the old `test-oop-extensions/`
  path.
- Workaround: took the bug number from the fix's commit message.

## Question: were the post-fix annotations on trunk?

- Question: "after the fix landed, on which trees were jobs still annotated on this bug?"
- Command: `fx-tests intermittent --bug 1378104 --since 30 --tree all` gives totals per tree and
  per platform, but not per date; needed `--json` and `.occurrenceRows[] | {pushTime, tree}`.
- What the output could have shown: a per-tree daily history, or the occurrence rows with push
  time and tree.
