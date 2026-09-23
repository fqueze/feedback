## Question: "in how many of this test's failing jobs did the suspected leaker fail too?"

- Commands: `fx-tests test <my test> --task-ids --limit 0` and `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js --task-ids --limit 0`, then extracting the task IDs from both with `rg -o` and `comm -23`.
- The answer (31 of 31) needed a script over two outputs. `fx-tests test <path> --with <other path>` (or a "failed in the same job" column naming the first test that failed earlier in the same job) would answer the leaker-or-victim question directly, which the skill asks for in every cascade.

## Messages that differ only by a generated ID are not grouped

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js --task-ids --limit 0`
- Expected: one issue row "uncaught rejection: PageActions: No anchor node for _<id>_" with ~44 failures.
- Got: 44 rows of `1x`, one per extension UUID (`_0cb20b0b-0e41-..._`, `_abceb13b-..._`, ...), which reads as 44 distinct failure modes and hides that this is the test's main one. Normalizing UUIDs (and similar hex/number IDs) before grouping would fix it.

