## The minidump of a crash in the same job (browser_dbg-sourcemapped-scopes.js)

- Question: the minidump ID of the `CRASH @ mozilla::(anonymous namespace)::RunWatchdog` that `fx-tests task TvIPAAEpR92HGf5U1ZXQfw --profiles` lists for browser_dbg-sourcemapped-toggle.js, to read the hung browser's thread stacks.
- Commands: `fx-tests task TvIPAAEpR92HGf5U1ZXQfw --profiles`, `fx-tests task ... --messages`, `fx-tests test devtools/.../browser_dbg-sourcemapped-toggle.js --task-ids --limit 0`.
- Expected: the dump ID next to the CRASH row (the guide says `test --task-ids` gives one "where the dump was uploaded").
- Got: the signature only; `test --task-ids` shows the task but no dump ID, although `public/test_info/7f0449d5-1ca4-85b4-2859-5c313787763a.dmp` is uploaded (the outcome is counted as TIMEOUT there, not CRASH).
- Workaround: `curl .../task/<id>/runs/0/artifacts?limit=1000` and grep for `.dmp`, then `fx-tests crash <task> <dumpId> --all-threads --limit 0`.
- What the output could have shown: the uploaded dump's ID on the CRASH line of `task`, and in `test --task-ids` even when the first outcome was TIMEOUT.

## `--bugs` with no bug found

- Command: `fx-tests test devtools/client/debugger/test/mochitest/sourcemaps/browser_dbg-sourcemapped-scopes.js --harness mochitest --bugs`
- Expected: a Bugs section, even if it says "no bug names this test".
- Got: the same output as without `--bugs`, so it is unclear whether the search ran.
