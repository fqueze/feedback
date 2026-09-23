## Run counts for a test on a try push disagree between views (browser_inspector_picker-page-reload.js)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids` (and with `--all-jobs --limit 0`)
- Expected: the `here` column to give failures over runs of the test on the push, and to say on which configs it ran.
- Got: `2/9` in both, while `fx-tests try <rev> --all-jobs --test <path>` reports 21 job runs on 7 configs (2 passed on retry). It is unclear what the 9 counts, and the default view does not say where the test ran, which the brief needs for a skipped test.
- Workaround: `fx-tests try <rev> --all-jobs --test <path>`, which answered the question directly.
