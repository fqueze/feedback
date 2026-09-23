## `test --bugs` prints no Bugs section at all when nothing matches

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_migrate_v6.js --bugs`
- Expected: a "Bugs" block, or a line saying no sheriff-annotated bug names the test (as `intermittent --test` does).
- Got: the normal `test` output, identical to running without `--bugs`; nothing on stderr either. Could not tell "no bug" from "flag ignored / lookup failed".
- Workaround: `fx-tests intermittent --test <path> --since 21`, which says explicitly that no bug names the test.

## `failures --message` does not search crash signatures, and blames a typo

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests behind that message (it is the test's #1 issue in `fx-tests test`).
- Got: "No failure matched ... Check --path, --message and --component for typos." The same string is found by `fx-tests crashes --signature "hang at shutdown"` (19,349 crashes, 503 tests).
- Workaround: `crashes --signature`. Suggest: when `--message` matches a crash signature, point to `crashes --signature`, or include CRASH rows.

## `crashes --signature` cannot list the tests or configs behind a signature

- Question: "which tests, on which configs, crash with `child process hang at shutdown`?" (to tell a tree-wide mac-debug problem from a test-specific one).
- Command: `fx-tests crashes --harness xpcshell --signature "hang at shutdown"` gives only the totals (19,349 crashes, 503 tests); there is no `--tests`, and `--config` is refused.
- Workaround: none within fx-tests; inferred the config from one test's `fx-tests test` table and from `fx-tests task` of one job (91 tests crashed that way in it).
