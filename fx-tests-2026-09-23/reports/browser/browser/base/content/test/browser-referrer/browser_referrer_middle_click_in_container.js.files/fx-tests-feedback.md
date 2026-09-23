## Question: which bugs name this test?

- Command: `fx-tests test browser/base/content/test/browser-referrer/browser_referrer_middle_click_in_container.js --bugs` (and the same for the old `referrer/` path)
- Expected: a list of bugs naming the test, as the brief says `--bugs` finds them.
- Got: the normal `test` output with no bugs section at all; in `--json`, `annotatedBugs` is `null`. Bugzilla has an open bug naming the test in its summary (bug 1972455, "almost perma ubuntu 24.04 ... browser_referrer_middle_click_in_container"), plus resolved ones (1776007, 1611026, ...).
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_referrer_middle_click_in_container`.
- What would have answered it: a "Bugs" section (or an explicit "no bugs found") when `--bugs` is passed, including bugs filed under the old path of a moved test.
