## `fx-tests test <path> --bugs` prints nothing about bugs
- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_migrate_v2.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test".
- Got: output identical to the same command without `--bugs`; no line about bugs at all, so "none found" and "flag ignored" look the same.
- Workaround: Bugzilla REST quicksearch by hand.

## Question: "do crashed tests tend to be the short ones?" (fx-tests task)
- Command: `fx-tests task <id> --passed` / `--json`.
- Expected: per-test durations next to outcome, to see that the crashes are short tests.
- Got: no duration column; used the resource-usage profile's `test` markers via profiler-cli `--json` plus a script.
- What could answer it: a duration column (first run and harness rerun) in `fx-tests task`.
