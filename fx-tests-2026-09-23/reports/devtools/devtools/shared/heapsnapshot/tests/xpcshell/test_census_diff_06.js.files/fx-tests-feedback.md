# fx-tests feedback (test_census_diff_06.js)

## `--bugs` prints nothing when it finds no bug

- Command: `COLUMNS=250 fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_06.js --bugs`
- Expected: a `Bugs` section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`. Nothing says whether the search ran and found nothing, or did not run.
- Workaround: grepped the output for "bug", found nothing, and treated it as "no bug".

## "Did this job's chunk contain test X?" has no answer when X did not fail

- Question: whether the other msix jobs where every launch failed had this test in their chunk. That would explain why only 1 of about 14 such jobs lists it.
- Command: `fx-tests task <taskId> --limit 0`, then grepping for `devtools/shared/heapsnapshot`.
- Got: only the failed tests are listed, so the answer is "no" only by inference: after the break every test fails, so a test that ran there would show up.
- Could have shown: a way to look up one test's outcome in a job, PASS or SKIP included, such as `fx-tests task <taskId> --test <path>`.
