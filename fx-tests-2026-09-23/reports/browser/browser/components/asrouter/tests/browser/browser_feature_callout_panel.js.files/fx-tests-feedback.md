## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/asrouter/tests/browser/browser_feature_callout_panel.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the normal `test` output, with no bug section at all and nothing on stderr. Same exit code (0). I could not tell "no bug" from "flag ignored / lookup failed".
- Workaround: Bugzilla REST `quicksearch=<file name>` via curl.
