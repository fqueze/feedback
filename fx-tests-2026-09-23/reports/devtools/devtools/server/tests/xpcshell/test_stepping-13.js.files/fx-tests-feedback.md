## `fx-tests test <path> --bugs` says nothing when no bug is found

- Command: `fx-tests test devtools/server/tests/xpcshell/test_stepping-13.js --bugs`
- Expected: a "Bugs" section, reading "none found" when there is none.
- Got: the same output as without `--bugs`, with no bug section at all. Nothing shows whether the lookup ran and found nothing, or did not run.
- Workaround: took the job-level bug (1991833) from `fx-tests intermittent` and from a sibling report.
