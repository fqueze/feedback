## `test --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/places/tests/browser/browser_history_sidebar_search.js --bugs`
- Expected: a "Bugs" section, or a line saying no sheriff-annotated bug names this test.
- Got: the same output as without `--bugs`. No section, no "none" line. So "no bug" looks the same as "the lookup silently failed".
- Workaround: Bugzilla REST `bug?summary=browser_history_sidebar_search`, which found only bugs closed in 2012–2016.

## `task --messages` lists a TEST-KNOWN-FAIL next to the real failure

- Command: `fx-tests task RqreuoDgRjGrccuQV57UCw.0 --messages`
- Expected: only unexpected messages, or known-fail ones labelled as expected.
- Got: `1x changed preference: browser.toolbarbuttons.introduced.sidebar-button` in the same list as the `Uncaught exception` failure. In the per-test profile that marker is `TEST-KNOWN-FAIL` (Status FAIL, Expected FAIL). The summary also counts it: "(+1 more message for this test)".
- Workaround: check the marker's Expected field in the profile.
