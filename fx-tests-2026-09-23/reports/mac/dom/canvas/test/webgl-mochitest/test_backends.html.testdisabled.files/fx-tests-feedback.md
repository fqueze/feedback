## Question: which bugs name this test?

- Command: `fx-tests test dom/canvas/test/webgl-mochitest/test_backends.html --bugs` (and `--bugs --json`, where `bugs` is absent and `annotatedBugs` gives nothing usable)
- Expected: the bugs naming the test, or an explicit "none found". Bugzilla has six, all closed; bug 1677803 is this exact failure mode, duped to the closed tracking bug 1777064.
- Got: the same output as without `--bugs`, with no bug section, so "no bugs" cannot be told from "the flag did nothing".
- Workaround: Bugzilla REST `bug?summary=test_backends.html`.
