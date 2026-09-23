## `Issues (first failure per run)` counts a TEST-KNOWN-FAIL message as a run's first failure

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_groups_tabContextMenu.js`
- Expected: one Issues row for the one unexpected failure all 97 jobs share, `group2 menu item has correct label - null == "Unnamed group"`.
- Got: two rows, 58x for that message and 51x for `handleEvent() was unable to perform a11y checks on hidden node: id: context_moveTabToNewGroup ...`. In the macOS profile (task Srb8In8aSAyPGfnebim12A) that a11y message is logged as TEST-KNOWN-FAIL, in an earlier subtest than the real failure. `fx-tests task <id> --messages` also lists it with the failures, with nothing saying it was expected.
- Workaround: ran `fx-tests task <id> --messages` on all 41 tasks behind `--issue 3` and grepped: every one also has the label failure. That took 41 calls.
