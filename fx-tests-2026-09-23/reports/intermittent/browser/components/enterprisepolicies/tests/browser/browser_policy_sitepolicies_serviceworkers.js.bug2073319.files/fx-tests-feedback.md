## Question: "which annotated jobs carry which extra failure lines?"

- Command: `fx-tests intermittent --bug 2073319 --limit 0`, then `--json` and a script over `occurrenceRows[].lines`.
- Why: the text output counts messages per annotated job (for example, 6x `tab missing output line for total leaks!`) and lists occurrences, but does not say which occurrences carry which message. I needed to know whether the leak lines came with the test's own failure, and in which jobs (all 6 were debug jobs that also had the BFCache timeout).
- What the default output could show: a short message tag per occurrence row, or `--message <substring>` to filter occurrences.

## `fx-tests test --task-ids --issue <n>` reprints the full per-config table every time

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 4` (and again with `--issue 5`).
- Expected: the task list for that issue.
- Got: the whole 36-row configuration table and the issues list again, before the task list, for each issue.
- Workaround: read from the "Task IDs" line down.
