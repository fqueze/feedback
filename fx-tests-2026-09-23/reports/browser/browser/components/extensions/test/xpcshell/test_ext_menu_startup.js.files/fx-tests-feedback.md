# fx-tests feedback

## Is there a bug for this test?
- Command: `fx-tests test browser/components/extensions/test/xpcshell/test_ext_menu_startup.js --bugs`
- Expected: a "Bugs" section, or an explicit "no annotated bugs" line.
- Got: output identical to the run without --bugs; no mention of bugs at all (JSON has `annotatedBugs: []`). Silent absence reads like the flag was ignored.
- Workaround: `--json` to see `annotatedBugs`, then a Bugzilla summary search (found bug 1903651, RESOLVED INCOMPLETE).

