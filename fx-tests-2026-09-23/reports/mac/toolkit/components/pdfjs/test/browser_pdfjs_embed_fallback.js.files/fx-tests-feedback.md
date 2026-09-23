## Question: on which tree and revision did each failing task run? (did any failure come after the fix landed?)

- Command: `fx-tests test toolkit/components/pdfjs/test/browser_pdfjs_embed_fallback.js --task-ids --limit 0`
- Expected: the tree and revision (or at least the task's creation time) next to each task id, so "the last failure was on the fix's parent revision" can be read off directly.
- Got: day, task id and job name only; the `--json` `taskIds` entries have no revision/tree either. `fx-tests task <id>` gives the revision, but one call per task (29 tasks here).
- Workaround: piped `--json` task ids into `curl .../api/queue/v1/task/<id>` and read `payload.env.GECKO_HEAD_REPOSITORY` / `GECKO_HEAD_REV` / `created`.
- What the output could show: a `tree rev` column in the `Task IDs` list (the task definition has it), and the last failing revision per tree in the summary.
