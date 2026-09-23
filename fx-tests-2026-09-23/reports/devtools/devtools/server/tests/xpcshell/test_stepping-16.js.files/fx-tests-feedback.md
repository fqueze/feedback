## One test's entry in a job with 1061 failures (test_stepping-16.js, 2026-09-22)

- Question: what does this job say about my test?
- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles | grep -A3 test_stepping-16.js` found nothing; the list is truncated, and my test is row ~150 of 1061.
- Expected: a `--test <path>` filter on `fx-tests task`, or the test named in `fx-tests test --task-ids` output with its message and profile.
- Workaround: `--limit 0` into a file, then grep.
