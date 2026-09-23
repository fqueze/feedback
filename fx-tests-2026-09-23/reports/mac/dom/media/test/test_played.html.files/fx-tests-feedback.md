# fx-tests feedback — test_played.html

## `test --bugs` prints nothing for a test whose tracking bug names it

- Command: `fx-tests test dom/media/test/test_played.html --bugs`
- Expected: bug 1110922, "Intermittent dom/media/test/test_played.html | single tracking bug" (NEW), which is also the bug on four of the test's `skip-if` conditions.
- Got: the ordinary `test` output with no bug section at all and exit 0 — no "no bugs found" line either, so it reads the same as the flag being ignored.
- Workaround: read the bug numbers from the manifest's `skip-if` comments and fetch them from Bugzilla.
- Possibly related: `fx-tests intermittent --bug 1110922 --since 21` finds no sheriff annotations, although the test failed 17 times in that window (the failures all passed on retry, so perhaps they were never starred). If `--bugs` only lists bugs that have annotations, saying so in the output would help.
