## Question: "does this manifest-level LeakSanitizer signature fire anywhere else on central?"

- Command: `fx-tests failures --harness mochitest --message GetServiceImpl --tests` and `--message LeakSanitizer`
- Expected: rows for manifest-level LSan failures (`TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | <manifest>.toml`), or a note that they aren't recorded.
- Got: `No failure matched.` It reads as if the signature doesn't exist tree-wide. Manifest-level leak failures are not in the data at all (same gap as the `task`/`try` entry in browser_console.js.files/fx-tests-feedback.md).
- Workaround: `fx-tests intermittent --bug <N> --since 60 --tree all`, which only works once you know the bug and only counts starred jobs.
