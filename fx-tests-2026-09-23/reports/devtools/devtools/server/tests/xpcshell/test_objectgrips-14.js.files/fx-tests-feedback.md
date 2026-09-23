## `fx-tests task --profiles` hides the test I came from in a job where most tests failed

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles`
- Expected: the row for `devtools/server/tests/xpcshell/test_objectgrips-14.js`, the test I asked `fx-tests test` about.
- Got: the first ~20 failing tests alphabetically, then `… 468 more`; the row is at line 559 of the `--limit 0` output.
- Workaround: `--limit 0` for each of the 6 jobs, then grep.
- Would help: `fx-tests task <id> --test <path>`, as `test_objectgrips-04.js.files/fx-tests-feedback.md` also asked.
