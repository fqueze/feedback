## Question: per-config and per-day counts of one failure mode

- Question: for one row of the Issues block (here row 2, `NS_ERROR_FILE_DIR_NOT_EMPTY`, 273 runs), which configs and days does it fail on, and at what rate? Here it mattered that the mode never fails on debug builds.
- Command: `fx-tests test browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon_onBrowserReady.js --task-ids --issue 2 --limit 0`, then an awk script over the task list to count per config and per date.
- What would have answered it: `--issue <n>` also filtering the "Failing configurations" table (with the run counts as denominators) and `--history`, e.g. `fx-tests test <path> --issue 2 --history`.
