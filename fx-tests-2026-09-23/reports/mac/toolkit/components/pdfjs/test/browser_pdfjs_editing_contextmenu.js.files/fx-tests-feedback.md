## Question: which failing tasks belong to which failure mode?

- Command: `fx-tests test toolkit/components/pdfjs/test/browser_pdfjs_editing_contextmenu.js --task-ids --limit 0`
- Expected: each task labelled with the Issues row it belongs to, or the list grouped under issue headers.
- Got: one "Task IDs" list whose dates restart from 2026-09-01 midway (it is grouped by issue, but without saying so), so a TSan stale-frame failure and a Mac timeout on the same day are indistinguishable except by config name.
- Workaround: rerun once per issue with `--issue 1`, `--issue 2`, `--issue 3`. The unfiltered list could print the issue header (as `--issue <n>` already does) before each group.
