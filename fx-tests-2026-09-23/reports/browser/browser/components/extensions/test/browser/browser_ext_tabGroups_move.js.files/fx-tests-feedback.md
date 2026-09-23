## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_tabGroups_move.js --bugs`
- Expected: a "Bugs" section, or a line saying no sheriff-annotated bug names this test.
- Got: output identical to the run without `--bugs`, so I could not tell "no bug" from "the flag did nothing / the query failed silently".
- Workaround: queried Bugzilla REST directly (`/rest/bug?summary=browser_ext_tabGroups_move`), which found two RESOLVED tracking bugs (1966823, 1968897).
