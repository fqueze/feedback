## Question: which tasks ran this test on this try push (so I can open one's profile)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/inspector/fonts/test/browser_fontinspector_all-fonts.js --task-ids`
- Expected: the task IDs behind each config row (3 jobs per config), so a passing run on a formerly skipped config can be linked and opened.
- Got: only the per-config counts table; `--task-ids` is silently ignored in `--test` mode, and `--json` has no task IDs either.
- Workaround: look the tasks up through the Treeherder API.

