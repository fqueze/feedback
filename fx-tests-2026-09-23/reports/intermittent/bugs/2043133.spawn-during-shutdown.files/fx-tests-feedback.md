## Question: "on which configs does this one failure mode happen, and at what rate?"

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_context.js --task-ids --issue 3 --limit 0`
- Expected: the per-config table (fails, runs, rate) restricted to Issues row 3, as the default output gives for all failures together.
- Got: a per-day list of 195 task IDs with their job names. The config table stays the all-modes one, where the macOS timeouts dominate and hide that this mode is Android Fission only (never `-nofis`).
- Workaround: parsed the task list with awk to count jobs per config, then divided by hand by the runs column of the all-modes table. `--config nofis` / `--config android --exclude-config nofis` confirmed the split through their Issues rows.

## `--bugs` prints nothing about bugs

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_context.js --bugs` (exit 0, stdout and stderr captured together).
- Expected: the sheriff-annotated bugs naming the test, or a line saying there are none.
- Got: the normal output, with no bugs section and no message at all.
- Workaround: `fx-tests intermittent --bug 2043133`, which showed that none of the bug's 23 recent annotations is this failure mode.
