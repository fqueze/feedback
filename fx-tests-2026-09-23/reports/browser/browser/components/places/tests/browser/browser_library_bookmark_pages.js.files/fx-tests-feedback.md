## `fx-tests test <path> --bugs` prints no bug section when none is found
- Command: `fx-tests test browser/components/places/tests/browser/browser_library_bookmark_pages.js --bugs`
- Expected: a "Bugs" section, or a line saying no open bug names the test.
- Got: the same output as without `--bugs`; nothing says whether the lookup ran or found nothing.
- Workaround: Bugzilla REST `summary=<test file name>` query (found only closed bugs, e.g. 1882156).
