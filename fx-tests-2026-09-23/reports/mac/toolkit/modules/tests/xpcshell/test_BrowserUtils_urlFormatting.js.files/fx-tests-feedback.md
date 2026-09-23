## `test --bugs` with no bug says nothing
- Command: `fx-tests test toolkit/modules/tests/xpcshell/test_BrowserUtils_urlFormatting.js --bugs`
- Expected: a line such as `Annotated bugs: none`.
- Got: exactly the output you get without `--bugs`, so "no bugs" looks the same as "the flag was ignored". I had to check `--json` (`annotatedBugs: []`) to be sure.
- Workaround: `--json`, then read `annotatedBugs`.

## Question: "how did my test fail in this job?" in a mass-failure job
- Command: `fx-tests task K3sp_FuDSjqpb-OqitSHzA --profiles` (591 failing tests)
- Expected: a way to show just one test's row, e.g. `--test <path>`.
- Got: the list is truncated alphabetically, so the test I asked about is not in the default output. The first `rg` over the default output found nothing, which reads as "not in this job".
- Workaround: `--limit 0 | rg -A6 <test name>`.
- Also: when most of a job's tests fail, a header line such as "589 of 729 tests timed out: job-wide breakdown" would help. Without it, the first rows read as though they were failures of their own.
