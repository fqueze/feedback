## `fx-tests test <path> --bugs` is silent when no bug names the test

- Command: `COLUMNS=250 fx-tests test devtools/server/tests/xpcshell/test_front_destroy.js --bugs`
- Expected: a "Bugs" section, saying "none found" when no bug names the test.
- Got: output identical to the run without `--bugs`, so "no bug names this test" reads the same as "the flag was ignored".
- Workaround: took it as "none", and looked up the job-level bug (1991833) from sibling reports.
