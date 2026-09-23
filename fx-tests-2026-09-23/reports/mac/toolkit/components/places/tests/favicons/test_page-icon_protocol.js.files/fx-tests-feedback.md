## Question: "is there a bug on file for this test?"

- Command: `fx-tests test toolkit/components/places/tests/favicons/test_page-icon_protocol.js --bugs`
- Expected: a Bugs section listing the bugs naming the test, or an explicit "no bug names this test".
- Got: exactly the output of `fx-tests test <path>` without `--bugs`; no Bugs section, no "none" line. I could not tell "no bug" from "flag ignored".
- Workaround: Bugzilla REST `bug?summary=test_page-icon_protocol`, which found three old closed bugs (1284965, 1799791, 1897148) and none open.
- What would have answered it: print the bugs found, including closed ones with their resolution, or "no bug names this test" when there are none.
