## Question: which tests did one crash signature hit, per day?

- Command: `fx-tests crashes --harness mochitest --path browser/components/aiwindow/ui/test/browser --signature mozalloc_handle_oom --day 2026-09-05`
- Expected: the tests behind the signature, with their counts (this is how to see an OOM moving from one test of a manifest to another over days).
- Got: only `crashes`/`tests`/`dumps` totals per signature. The test names are in `--json` (`rows[].tests[]`), so I looped over `--day` with a Python one-liner.
- What would have answered it: a `--tests` flag (or the test list printed under each row when `--signature` narrows to one), and ideally a per-day breakdown in one call instead of one call per `--day`.

## `fx-tests task --messages` hides which runs' messages belong to which execution

- Command: `fx-tests task GAtr7Lp7SsKA9TmB6kKrhA.0 --messages` and `Ydszy9LkTeGpxeY16JqZXg.0`
- Got: `CRASH, TIMEOUT — 9 failing executions of 9` (GAtr7) and `8 failing executions of 9` (Ydszy) for a test that ran twice (first run + retry). The resource-usage profile shows one TIMEOUT, then `processing 7 crashes`, then a passing retry.
- Expected: 1 failing execution of 2. The count seems to include each crash dump processed after the timeout as an execution.
