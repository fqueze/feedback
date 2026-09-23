## `fx-tests test` splits one failure mode into one Issue row per extension UUID

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js --task-ids --limit 0`
- Expected: one Issues row, `45x uncaught rejection: PageActions: No anchor node for _<uuid>_`, next to `3x TIMEOUT`.
- Got: 47 rows, 46 of them `1x ... No anchor node for _68216625-..._`, `1x ... _4035d3d8-..._`, etc. The message carries the test extension's random ID, so every run is its own "issue", and the list reads as 46 different failure modes. It cost ~2,500 tokens of output and a manual count.
- Workaround: `rg -c 'No anchor node'` on the saved output.
- Suggestion: normalize UUIDs (and `{uuid}` / `_uuid_` forms) in messages before grouping, as for numbers.

## The failing jobs' revisions, to date them against a fix

- Question: was each failing job of this test on a revision pushed before bug 1378104's landing?
- Command: `fx-tests test <path> --task-ids --limit 0 [--json]`
- Got: task ID, job name, chunk, day. No revision, so the answer needs one `fx-tests task <id>` per job (which prints `autoland <rev>`), then an hg-to-git lookup per revision.
- Could have shown: the repo and revision (and push time) per task ID, in both text and `--json`.
