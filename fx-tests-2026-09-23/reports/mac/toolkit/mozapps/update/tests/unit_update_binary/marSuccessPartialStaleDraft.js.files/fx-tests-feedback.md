## Which of this test's failing jobs also had a sibling test fail

- Command: `fx-tests task <id> --profiles` for each of 13 task ids, then `rg` over the saved outputs for `unit_update_binary` paths.
- Question: "for the failing jobs of this test, which other tests failed in the same job, and how often" — the co-failure table.
- Expected: `fx-tests test <path> --task-ids` (or a `--cofailures` flag) to list, per failing job or aggregated, the other tests that failed in it.
- Got: only the task ids; one `fx-tests task` call per job.

## Tests running at the same time as this one, on a config where it passes

- Question: "on bare metal, where this test never fails, which tests run alongside it, and how long does it take there".
- Command: none answered it. `fx-tests test --durations` gave the per-config medians, but neighbours in time need a resource-usage profile of a passing job, and `fx-tests` lists task ids only for failing runs.
- Expected: a way to get a few task ids of passing runs for a config (e.g. `fx-tests test <path> --task-ids --status pass --config <c> --limit 3`), to load their resource-usage profiles.
- Workaround: reused the resource-usage JSON another report had saved for two bare-metal jobs where a sibling test failed.
