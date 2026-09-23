# fx-tests feedback

## Question: on which configs does one failure mode fail, when its message embeds a temp path?

- Command: `fx-tests test toolkit/components/places/tests/unit/test_PlacesSemanticHistoryManager.js --task-ids --limit 0`, then `--issue 6`, `--issue 29`.
- Expected: the `Could not open connection to <tmp>/places_semantic.sqlite` family (143 rows, "↑ same as 6, but to …") counted as one issue, with its configs.
- Got: 143 separate 1x issues; `--issue <n>` returns the single task of one row, so the family's per-config breakdown is not reachable without reading all 143.
- Workaround: `--json`, then a Python script over `taskIds[].message` matching `Could not open connection`, counting `jobName`.

## Question: which failing tests does a task have (default listing truncates)?

- Command: `fx-tests task TaPfeICTRBWEIg7nNgLRPg --profiles | rg PlacesSemanticHistoryManager`
- Expected: the test's entry (it failed in that job).
- Got: nothing; the list stops at 20 tests with `… 7 more (--limit 0 for all)` at the very end, after the part a grep would look at.
- Workaround: `--limit 0`. Documented, but easy to miss when grepping; a `--test <path>` filter on `task` would answer this directly.
