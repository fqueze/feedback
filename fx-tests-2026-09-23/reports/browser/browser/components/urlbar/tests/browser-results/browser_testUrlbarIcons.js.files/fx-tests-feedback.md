## `test --bugs` prints nothing when no bug is annotated

- Command: `fx-tests test <path> --bugs`
- Expected: a line such as "Annotated bugs: none".
- Got: the same output as without `--bugs`; only `--json` shows `annotatedBugs: []`, so "no bug" looked like "flag ignored".
- Workaround: `--bugs --json | jq .annotatedBugs`.

## Issues block does not normalise `blob:system/<uuid>` or long `data:` URIs

- Command: `fx-tests test browser/components/urlbar/tests/browser-results/browser_testUrlbarIcons.js`
- Expected: one issue row, "121x Icon is search glass when urlbar is unfocused - <icon> == ...".
- Got: 114 rows ("↑ same as 2, but - blob:system/<uuid>..."), one per UUID, plus a 7 KB base64 `data:` URI printed in full (even with COLUMNS set), which floods every command's output (`--history`, `--task-ids`, `--bugs` all repeat it).
- Workaround: `--json` and group by the text before ` - ` in a script.

## No revision per failing task in `test --task-ids`

- Question: "what is the last failing revision", to find the landing that stopped the failure.
- Command: `fx-tests test <path> --task-ids --limit 0` (text and `--json`): task, job and day only.
- Workaround: `fx-tests task <id> --json | .revision` once per task (22 calls for one day), then lando hg2git.
- Could have shown: the push revision (and push time) per task, or a "last failure: <repo> <rev> <time>" line in `--history`.
