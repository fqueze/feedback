## `fx-tests test <path> --bugs` printed no bug section

- Command: `fx-tests test netwerk/test/unit/test_webtransport_simple.js --bugs`
- Expected: the bugs naming the test; Bugzilla has bug 1816439 "Intermittent netwerk/test/unit/test_webtransport_simple.js | single tracking bug" (ASSIGNED) plus 3 resolved ones.
- Got: the same output as without `--bugs`, with no bug section and no "no bugs found" line.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=test_webtransport_simple.js`.
