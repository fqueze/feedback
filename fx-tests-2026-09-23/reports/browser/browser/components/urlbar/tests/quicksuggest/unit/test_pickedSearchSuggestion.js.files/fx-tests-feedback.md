## `failures --message` silently misses crash signatures

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests behind the CRASH issue that `fx-tests test <path>` lists as `CRASH child process hang at shutdown`, or a hint that crash signatures live in `fx-tests crashes`.
- Got: "No failure matched. … Check --path, --message and --component for typos."
- Workaround: `fx-tests crashes --harness xpcshell --signature "child process hang"`.

## `test --bugs` prints nothing about bugs when there are none

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_pickedSearchSuggestion.js --bugs`
- Expected: a "Bugs: none found naming this test" line.
- Got: the same output as without `--bugs`, so it is unclear whether the lookup ran.
- Workaround: Bugzilla REST quicksearch.
