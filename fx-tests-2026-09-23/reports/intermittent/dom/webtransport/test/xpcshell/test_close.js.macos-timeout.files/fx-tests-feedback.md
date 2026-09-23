## Question: which bugs name this test?

- Command: `fx-tests test dom/webtransport/test/xpcshell/test_close.js --bugs`
- Expected: a Bugs section listing bug 2070388 and bug 1982953 (both have the path in their summary), or "no bug names this test".
- Got: the same output as without `--bugs`; the word "bug" appears only inside the skip-if annotations. Nothing says whether a search ran.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?quicksearch=test_close.js%20webtransport'`.

## Question: what else timed out in the jobs where this test timed out?

- Command: `fx-tests test <path> --task-ids --issue 8 --limit 0`, then `fx-tests task <id> --profiles` for each of the 21 tasks, then awk over the saved outputs to count the TIMEOUT tests.
- Expected: one command that gives, for one failure mode, the other failures in the same jobs, ranked by how many of those jobs they appear in. That is the "machine-wide or test-specific?" question.
- Got: nothing in `fx-tests test` answers it; 21 `fx-tests task` calls did.
- What could have shown it: `fx-tests test <path> --issue <n> --co-failures`.
