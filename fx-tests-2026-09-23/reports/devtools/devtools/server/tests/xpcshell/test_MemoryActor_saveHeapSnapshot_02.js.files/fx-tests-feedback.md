## Which tests share this crash signature?

- Command: `fx-tests failures --harness xpcshell --message 'child process hang at shutdown'`
- Expected: the 507 tests `fx-tests crashes --harness xpcshell` lists for that signature, or a pointer to `fx-tests crashes`.
- Got: "No failure matched. Searched 4,986 tests ..." even though the signature has 19,591 crashes in the same window; crash signatures are not searched by `--message`.
- Workaround: `fx-tests crashes --harness xpcshell` (which gives counts but not which tests, nor per-config).

## `--bugs` printed nothing

- Command: `fx-tests test devtools/server/tests/xpcshell/test_MemoryActor_saveHeapSnapshot_02.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, with no line about bugs at all, so "none found" and "flag ignored" look the same.
- Workaround: `fx-tests intermittent --test <path>` (which does say none), plus Bugzilla REST searches.
