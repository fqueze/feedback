## Unlabelled second section in `--task-ids --issue` output

- Command: `fx-tests test browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon.js --task-ids --limit 0 --issue 4`
- Expected: one list of task IDs under `Task IDs (345 jobs, issue 4: …)`, or a labelled second list.
- Got: 320 task IDs by date (2026-09-14 … 09-21), then a second run of date headers (2026-09-14 … 09-21) with 29 more task IDs and no heading. `--json` shows the second group is the `FAIL-SEQUENTIAL` rows (and the header says 345 jobs while the JSON has 349 rows).
- Workaround: `--json` and read `taskIds[].status`.

## Question: "how often does this one failure mode fail in the parallel phase vs on the sequential retry?"

- Command: `fx-tests test <path> --task-ids --limit 0 --issue <n> --json`, for n in 3 4 6 7 8, then a script counting `status`.
- The text output could have shown, per issue, the FAIL-PARALLEL / FAIL-SEQUENTIAL split (it has it per row). Here it matters: 74 of the 75 sequential failures of the whole test are this one mode, which the aggregate "fails almost only in parallel" line hides.

## Question: "which configs does this failure mode hit, when it is split across 'same as 3, but …' variants?"

- Command: same as above, then a script merging issues 3, 4, 6, 7, 8 by job name (stripping the chunk suffix) and dividing by `configs[].runCount`.
- The five Issues rows differ only by the built-in add-on version inside the message (157.0.0, 157.1.0, 158.0.0, 158.1.0, 158.2.0). An `--issue 3+` / "include the ↑ variants" option, with a per-config count and rate, would have answered it directly.
