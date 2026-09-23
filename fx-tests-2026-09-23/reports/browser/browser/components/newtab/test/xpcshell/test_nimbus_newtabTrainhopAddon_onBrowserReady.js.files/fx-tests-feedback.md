## Question: "on which configs, and in which execution mode, does this one failure mode fail?"

- Command: `fx-tests test browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon_onBrowserReady.js --task-ids --issue 1 --limit 0 --json`, then a Python script counting `taskIds[].jobName` and `taskIds[].status`.
- Expected: the "Failing configurations" table (and the parallel/sequential line) restricted to Issues row 1 when `--issue 1` is given. The test has two modes with different config profiles: the worst config overall (windows11-64-25h2-ccov, 39.7%) has 1 of the 553 `SharedDataMap: in shutdown` failures; its failures are the other mode.
- Got: `--issue` only filters the task-ID list; the config table and the verdict still mix both modes. The text task-ID list is grouped by day, so the per-config count means counting lines by hand.
- Workaround: the script over `--json`.

## Question: "when did this failure mode first appear?"

- Command: `fx-tests test <path> --day <d>` for each day, reading the Issues block, until the message disappeared (09-08: absent, 09-09: 8x).
- Expected: `--history --issue <n>` giving per-day counts for one mode. The overall history hid the step change: the other mode is steady at 10–23 a day, so the start date was not visible in the totals.
- Got: `--history` counts all modes together; `--history --issue 1` exits 1 with "--issue selects which failure the printed task IDs belong to, so it needs --task-ids".
- Workaround: one `--day` call per day.
