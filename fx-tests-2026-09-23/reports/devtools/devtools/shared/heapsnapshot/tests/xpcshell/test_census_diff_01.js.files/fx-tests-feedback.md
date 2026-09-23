## `test --bugs` prints no bug section when there is none

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_census_diff_01.js --bugs`
- Expected: a line such as `Bugs: none found naming this test`.
- Got: the same output as without `--bugs`. I could not tell whether it searched and found nothing, or ignored the flag.
- Workaround: grepped the output for "bug", then searched Bugzilla myself.

## `task --profiles`: one test's row in a job with 1,154 failures

- Question: "what does this job say about my test?"
- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles`
- Expected: a way to go straight to one test's row (a `--test <path>` filter), or my test's row listed first when I came from `fx-tests test`.
- Got: the list is truncated by default, so my test was not shown. I needed `--limit 0` (4,634 lines) and grep.
- Workaround: `--limit 0 > file; rg census_diff_01 file`.
