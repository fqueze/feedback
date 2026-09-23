## `fx-tests test <path> --bugs` prints no bugs section at all

- Command: `fx-tests test browser/components/places/tests/unit/test_browserGlue_distribution.js --bugs`
- Expected: the bugs naming the test (Bugzilla has 4, all resolved: 523936, 527199, 545323, 553885), or an explicit "no open bug names this test".
- Got: the same output as without `--bugs`; the word "bug" does not appear. It is impossible to tell "searched, found none" from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=test_browserGlue_distribution`.
