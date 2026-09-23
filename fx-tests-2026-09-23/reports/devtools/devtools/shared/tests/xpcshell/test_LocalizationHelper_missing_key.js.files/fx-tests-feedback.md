## Question: "which bug are this test's failing jobs starred on?" (test_LocalizationHelper_missing_key.js)

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_LocalizationHelper_missing_key.js --bugs`
- Expected: a "Bugs" section, saying "none name this test" if so, and ideally the bug the failing jobs were annotated with.
- Got: the same 17 lines as without `--bugs`, with no bugs section and no "none found" line, so I could not tell whether the flag did anything. The three failing jobs are the msix meltdown tracked in bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which I only found with a Bugzilla quicksearch on the traceback text.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?quicksearch=msix "Access is denied"'`. `fx-tests test` could print "no bug names this test", plus the bugs its failing task IDs were starred with.

## Question: "did the job-level failure stop, or did it stop hitting this test?"

- Command: `fx-tests test <path> --history` (no failure since 2026-09-07) next to `fx-tests intermittent --bug 1991833` (10 annotations 2026-09-16 to 09-22 on the same config).
- Expected: some way to see that the recent meltdown jobs did not run this test.
- Got: nothing links the two. I had to run `fx-tests task <id> --passed --limit 0` on 4 annotated jobs and grep for the test: it was absent from all 4 (other chunks).
- Workaround: as above. `fx-tests intermittent --bug` could list, per occurrence, whether a given test ran in that job.
