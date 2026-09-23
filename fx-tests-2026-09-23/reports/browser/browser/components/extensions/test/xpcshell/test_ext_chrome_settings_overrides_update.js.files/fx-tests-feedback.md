## `fx-tests test <path> --bugs` prints nothing when there is no annotated bug

- Command: `fx-tests test browser/components/extensions/test/xpcshell/test_ext_chrome_settings_overrides_update.js --bugs`
- Expected: a line such as `Bugs: none annotated in the window` (and ideally a pointer to any closed bug naming the test).
- Got: the same output as without `--bugs`; only `--json` showed `"annotatedBugs": []`. Without that, "no bug" and "the flag did nothing / the query failed silently" look the same.
- Workaround: `--json` plus a Bugzilla REST search on the summary, which found the RESOLVED INCOMPLETE tracking bug 1930057.
