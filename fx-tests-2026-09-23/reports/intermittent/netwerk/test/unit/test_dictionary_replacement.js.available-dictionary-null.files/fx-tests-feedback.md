## `--issue <n>` reprints the whole summary each time

- Command: `fx-tests test netwerk/test/unit/test_dictionary_replacement.js --task-ids --limit 0 --issue 4` (and the same for 2, 5 and 6)
- Expected: only the task IDs of that issue, since the summary was already read.
- Got: the verdict, the full failing-configurations table and the whole Issues list again before the task IDs, on every call. Four failure modes meant four full summaries.
- Workaround: none, I read past it.

## Question: "the failing tasks and configs of one failure mode whose line number shifted"

- Issues 2 and 6 (`: 313` / `: 320`) and 4 and 5 (`: 433` / `: 440`) are the same assertion, moved by a landing (bug 2050313). Nothing groups messages while ignoring the line number, and nothing gives per-issue config counts.
- Commands: `--task-ids --issue <n>` once per issue, then counting configs by hand from the task lists.
- What could have answered it: per-issue config counts in `--task-ids --issue`, and an option to merge issues whose messages differ only in `: <line>]`.
