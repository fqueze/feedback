## `fx-tests test <path> --bugs` with no matching bug prints nothing distinct
- Command: `fx-tests test browser/extensions/newtab/test/xpcshell/test_RecentSearchesFeed.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`; I had to diff by eye to conclude there is none.
- Workaround: none; assumed "none".
