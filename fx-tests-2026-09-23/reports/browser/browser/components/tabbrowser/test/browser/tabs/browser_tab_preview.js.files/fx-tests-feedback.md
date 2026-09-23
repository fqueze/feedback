## "Which jobs failed the harness rerun too?" needs a script

- Command: `fx-tests test <path> --task-ids --limit 0` (then `--json` and filtering `taskIds[].occurrences > 1`)
- Expected: the text listing to mark tasks where the test failed on both executions (the ones that have a `-2` profile), or a filter for them.
- Got: the text list shows every task with no occurrence count; the summary only says "95 of 1581 failing jobs saw the failure more than once".
- Workaround: python over `--json`.
- Question: "the jobs whose rerun failed too, per config" — that is where a fresh-browser profile exists, and for an order-dependent failure whether any exists at all.

