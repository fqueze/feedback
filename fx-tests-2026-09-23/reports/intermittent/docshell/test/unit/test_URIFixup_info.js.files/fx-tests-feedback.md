## Question: per-config rate of one failure mode before a step change

- Commands: `fx-tests test docshell/test/unit/test_URIFixup_info.js` (per-config table mixes the TIMEOUT and FAIL modes), then a shell loop over `--day 2026-09-0N --coverage --limit 0` for 9 days, summed with awk.
- Expected: a way to get the per-config fail rate of one Issue (`--issue 3`) over a date range (for example `--until 2026-09-09`), since the two modes here had disjoint dates and configs.
- Got: `--issue` only works with `--task-ids`. The `Failing configurations` table has only the configs that failed on that day, so a day's runs on a config that did not fail are missing from it. `--since` only counts back from the end of the window.
- Workaround: `--coverage` per day, summed by hand. Counting task IDs per config from `--task-ids --issue 3` gave the job counts but no denominators.

## `--task-ids --issue 2` lists each date group twice

- Command: `fx-tests test docshell/test/unit/test_URIFixup_info.js --task-ids --limit 0 --issue 2`
- Expected: one `2026-09-10` group and one `2026-09-11` group under `Task IDs (256 jobs, …)`.
- Got: `2026-09-10`, `2026-09-11`, then `2026-09-10`, `2026-09-11` again, with different task IDs and no label saying what splits them (551 rows for 256 jobs).
- Workaround: none needed for this report. Grepping for "the last task" was misleading, though.
