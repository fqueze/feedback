## Question: "what is the failure message of this test on my try push?"
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --messages --task-ids --full-messages --limit 0`
- Expected: the failure message(s) under the `browser_storage_cookies_sort.js` row of NEW INTERMITTENTS (the PERMA-FAILS rows do print one).
- Got: config, central rate and task IDs only; no message, even with `--messages`.
- Workaround: `fx-tests task KFHrpOMfT3-XfyW9tEMCEg --profiles --full-messages` per task.

## Question: "how many times did it fail, out of how many?"
- Command: same `fx-tests try` as above: the row says `6/7` on one config.
- `fx-tests try <rev> --test <path> --all-jobs` says 3 jobs on that config, 3 failed, 0 passed; `fx-tests task` says 2 failing executions of 2 in each. So 6 of 6 executions failed; what the 7th run is (task Awb0u_QTSB6WLDzPk3V71A.0, which has no profile?) is not said. A `6/7` next to "NEW INTERMITTENTS" reads as intermittent, when every execution failed.
