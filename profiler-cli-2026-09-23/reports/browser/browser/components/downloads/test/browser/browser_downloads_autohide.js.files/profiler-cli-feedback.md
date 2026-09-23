## Question: which tests ran in this browser before mine, with their status

- Command: `profiler-cli thread markers --session <s> --category Test --search name:test --list --limit 0`
- Expected: only the per-test `test` markers (`PASS — <path>`, `FAIL — <path>`), one per test.
- Got: 324 rows, because `name:` also matches Text markers' `name` payload (all the `TEST-PASS`, `task`, `TestUtils` rows). The guide warns about it, but there is no way to say "marker name exactly `test`".
- Workaround: piped the output through `grep -E '^  m-[0-9]+ +test '`.
- Could have shown: an exact-name match (e.g. `--name test`), or a `thread tests` summary for mochitest profiles.
