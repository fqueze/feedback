## Were the failures after a fix landed on revisions that contain it?

- Question: the test's failures stopped on 2026-09-02 when a fix landed, then 14 came back on 2026-09-14. Were those on revisions pushed after the fix, or jobs run later on old revisions?
- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_syncedtabs_sidebar.js --task-ids --limit 0`, then `fx-tests task <taskId>` for each of the 14 task IDs to read the revision, then `curl https://hg.mozilla.org/integration/autoland/json-pushes?changeset=<rev>` for each push date.
- Expected: `--task-ids` (or `--history`) grouping by the push date or push id of the revision, not only the day the job ran, or printing the revision next to each task ID.
- Got: task IDs grouped by the day the job ran, with no revision. All 14 of the 2026-09-14 failures turned out to be on autoland pushes from 2026-08-14/15 (pushes 275391-275403), i.e. old revisions retested; `--history` makes it look like the failure came back.
- Workaround: 25 `fx-tests task` calls plus 20 hg json-pushes calls.

## Chunk label differs between `test --task-ids` and `task`

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_syncedtabs_sidebar.js --task-ids --limit 0` vs `fx-tests task fD0D7jdSRoC0FgtYOZ7klw`
- Expected: the same job name.
- Got: `test --task-ids` prints `test-macosx1500-aarch64/opt-mochitest-browser-chrome-1` for fD0D7jdSRoC0FgtYOZ7klw (and `...-chrome` with no chunk for DCXHQkhfTNW-fWRdhutq9w, PZacRVq2SXKrVoCcyR4mTg), `task` prints `test-macosx1500-aarch64/opt-mochitest-browser-chrome-7` for all of them.
- Workaround: trusted `fx-tests task`.

## `--since` takes a day count while `--day` takes a date

- Command: `fx-tests test <path> --since 2026-09-03`
- Expected: the window from that date (the natural thing after reading a landing date).
- Got: `--since expects a non-negative integer`.
- Workaround: counted days back from the end of the window (`--since 18`).
