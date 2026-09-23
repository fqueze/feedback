## `fx-tests test <path> --bugs` prints no bugs section and no "none found"

- Command: `fx-tests test docshell/test/unit/test_URIFixup_info.js --bugs`
- Expected: a list of bugs naming the test (Bugzilla has at least bug 2030601 "Intermittent docshell/test/unit/test_URIFixup_info.js | single tracking bug"), or an explicit "no bugs found".
- Got: the ordinary `fx-tests test` output, with nothing about bugs on stdout or stderr.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=test_URIFixup_info.js'`.

## Question: "which bug does each failure mode of this test belong to?"

- Command: `fx-tests test <path>` lists the issues (FAIL `1 === null`, TIMEOUT), and `fx-tests intermittent --bug N` lists a bug's messages, but nothing maps one to the other.
- It could show: the bug sheriffs starred each issue on, next to the issue under Issues.
