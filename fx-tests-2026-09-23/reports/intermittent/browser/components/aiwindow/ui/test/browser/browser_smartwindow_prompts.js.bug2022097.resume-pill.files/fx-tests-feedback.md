## Question: on which configs does this one failure mode happen, with counts?

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 6` (and `--issue 7`, `--issue 15`).
- Expected: a per-config count for the issue (like the per-config table for the whole test).
- Got: only a dated list of task IDs with job names (chunk suffixes included); I counted configs by hand (opt 25, debug 14, ...).
- Also: the issue message has no subtest name (`Resume pill plus static starters - 0 == 4`), so which subtest fails needs a profile or a grep of the test file.
