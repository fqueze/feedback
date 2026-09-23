# fx-tests feedback — browser_net_filter-autocomplete.js

## Did the try push run the config the skip-if covered?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/netmonitor/test/filters/browser_net_filter-autocomplete.js --all-jobs`
- Question: the test's only skip is `http3`; did the push schedule any http3 job, and did the test run there?
- Expected: the table to say which scheduled configs did not run the test (e.g. "not scheduled on this push: *-http3" or "scheduled, test not run: manifest run-if").
- Got: only the 7 configs that ran it. Absence of http3 reads the same as "not scheduled" and "scheduled but filtered by the manifest".
- Workaround: `fx-tests try <rev> --all-jobs --json` and grep for `http3` in job names (only central-history mentions were found, so no http3 job was on the push). `--config` is refused on `try`, so it cannot be narrowed that way.

## `try --test --task-ids` prints no task IDs

- Command: same as above, plus `--task-ids` (and `--json`).
- Question: which task ran and passed it, to cite one.
- Expected: task IDs per row. Got: the same table, no IDs, also none in `--json`.
- Workaround: none; the report cites configs and counts only.
