## `fx-tests test --bugs` prints nothing about bugs when none match
- Command: `fx-tests test browser/base/content/test/referrer/browser_referrer_open_link_in_container_tab2.js --bugs` (exit 0)
- Expected: a Bugs section, or a line saying no sheriff-annotated bug names this test.
- Got: output identical to the run without `--bugs` (26 lines), so "no bugs" and "the flag did nothing" look the same.
- Workaround: queried Bugzilla directly (`/rest/bug?summary=browser_referrer_open_link_in_container_tab2`), which found only closed bugs.
