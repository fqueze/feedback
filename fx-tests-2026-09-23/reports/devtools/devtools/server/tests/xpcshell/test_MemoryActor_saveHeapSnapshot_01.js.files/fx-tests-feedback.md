## Is this failure mode specific to my test, or tree-wide?

- Command: `fx-tests test devtools/server/tests/xpcshell/test_MemoryActor_saveHeapSnapshot_01.js`
- Expected: next to the top issue (`CRASH child process hang at shutdown`, 111x), a hint that the same message hits many other tests on the same config.
- Got: nothing; I only found out from `fx-tests task <id>` (95 tests crashed the same way in one job) and `fx-tests crashes` (19,591 crashes across 507 tests).
- Workaround: `fx-tests crashes`, then `fx-tests test` on an unrelated test to compare configs.

## `failures --message` does not find crash signatures

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind that message, or a pointer to `fx-tests crashes --signature`.
- Got: `No failure matched. Searched 4,986 tests ...` although `fx-tests crashes` lists the signature with 19,591 crashes in 507 tests.
- Workaround: `fx-tests crashes`.
