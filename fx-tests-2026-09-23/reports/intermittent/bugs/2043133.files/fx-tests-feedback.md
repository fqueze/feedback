## `errors --message` undercounts a fatal assertion

- Question: "how many xpcshell jobs a day log `ASSERTION: Overwriting an existing document channel!`?"
- Command: `fx-tests errors --harness xpcshell --message "Overwriting an existing document channel"` (default day 2026-09-21), and `--day 2026-09-20`, `--day 2026-09-14`.
- Expected: roughly one occurrence per job that crashed this way.
- Got: 1 occurrence on 2026-09-21 and 2 on 2026-09-20; yet of 20 job logs from those two days I downloaded (from `fx-tests test ... --task-ids --issue 2`), 11 contain the assertion (8 of them on 2026-09-21).
- Workaround: downloaded the job logs and grepped them.
