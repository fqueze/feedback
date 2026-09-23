## Question: which bug are this test's failing jobs starred on?

- Command: `fx-tests test browser/components/places/tests/unit/test_PUIU_setCharsetForPage.js --bugs`
- Expected: bug 1991833 ("High frequency win-msix PermissionError: [WinError 5] Access is denied"), which sheriffs starred at least two of this test's failing jobs on (ZgUlY0ZlRvmHZNTOfia3AA.0 and OgH9QhqjS4a-6VFFNGQrEg.0 are in `fx-tests intermittent --bug 1991833`'s occurrences).
- Got: no bug listed; the bug's summary does not name the test, so the summary search cannot find it.
- Workaround: manual Bugzilla summary search for "Access is denied", then `fx-tests intermittent --bug 1991833` to confirm the task ids overlap. `--bugs` could also list the bugs the failing task ids were annotated with.
