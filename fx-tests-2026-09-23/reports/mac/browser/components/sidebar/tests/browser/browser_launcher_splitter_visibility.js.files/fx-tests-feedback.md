## Question: are these failures on current code, or on old revisions re-run later?

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_launcher_splitter_visibility.js --history` / `--task-ids`
- Expected: some sign that the failing jobs ran code weeks older than the day they are counted under (backfill / retrigger, push date).
- Got: 13 failures on 2026-09-02 and 7 on 2026-09-14, verdict "intermittent", worst config 2.3%. All 18 jobs turned out to be a `backfill-task` (created 2026-09-02) on autoland pushes from 2026-08-27, and a `retrigger-multiple-task` (2026-09-14) on pushes from 2026-08-15, all older than the fix (2026-08-28). The test has 0 failures on current code.
- Workaround: `fx-tests task <id>` for the revision, then hg `json-pushes?changeset=` for each push date, and the Taskcluster task definition (`tags.action`) for backfill vs retrigger. That took ~6 calls.
- What the output could show: the push date next to each task ID, a "backfill/retrigger of an older push" flag, and a verdict that excludes or tags jobs whose revision is older than the window.

## `--bugs` printed nothing

- Command: `fx-tests test <path> --bugs`
- Expected: bug 2035383 ("Intermittent browser_launcher_splitter_visibility.js | single tracking bug", RESOLVED FIXED 2026-08-28), or at least a "no annotated bugs in the window" line.
- Got: the normal output with no bugs section and no message at all.
- Workaround: found the bug from the fix's commit (`git log` on the test helper).
