## Question: "where is my test in this job's failure list"

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: the entry for the test I came from (`fx-tests test` pointed me at this task for it).
- Got: 488 failing tests, truncated to a first page that did not include it; needed `--limit 0` and a grep over ~1,000 lines.
- Workaround: `--limit 0 | grep -A5 <test>`.
- Output could show: a `--test <path>` filter on `fx-tests task`, or the entry for the path given to `fx-tests test --task-ids` when both are known.
