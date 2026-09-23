## Question: "how many distinct failure modes does this test have?"

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_multiselect_tabs_unload_telemetry.js --limit 0`
- Got: 167 Issues rows of `1x`, each the same assertion with different numbers embedded (`Memory should go down after unload (before: N, after N) - N < 100000`), so the list does not say that there is only one failure mode.
- Workaround: `--json`, then normalize digits in a script. Grouping messages after replacing long numbers with a placeholder would answer it directly.

## Question: "did the failures stop at the landing of revision X?"

- Command: `fx-tests test <path> --task-ids --limit 0` (and `--json`)
- Got: task id, job name, day. No revision or push time, so telling failures before a landing from those after it on the same day took one `fx-tests task <id>` per task plus a Treeherder push API call for each revision.
- A revision and push timestamp per task row would answer it.
