## The JS stack of an uncaught rejection

Question: "which code rejected, for an `uncaught rejection: …` failure?"

- Command: `fx-tests task IJZyGH09SdOiPtEsDiQXyg.0 --messages --full-messages`
- Expected: the rejection's stack, which xpcshell logs on the lines right after the `TEST-FAIL | … | uncaught rejection: Connection is not open.` line (`ensureOpen@…Sqlite.sys.mjs` ← `execute` ← `updateProfile` ← `saveUpdatesToDB` ← `setAvatar` ← `maybeSetupDataStore/<` ← `notify@…Timer.sys.mjs`).
- Got: only `1x uncaught rejection: Connection is not open.` The per-test profile does not have it either: its `TEST-UNEXPECTED-FAIL` marker's stack is the harness check (`PromiseTestUtils.sys.mjs!assertNoUncaughtRejections` ← `_execute_test`), not the rejection's.
- Workaround: downloaded `public/logs/live_backing.log` (900 KB) and grepped the lines after the `TEST-FAIL`.
- What could have shown it: the stack lines following a `TEST-FAIL` message, under `--messages`.

## `--bugs` prints nothing about bugs

- Command: `fx-tests test toolkit/components/nimbus/test/unit/shutdown/test_RSEL_updateRecipes_load_unenrolled_slugs.js --bugs`
- Expected: a Bugs section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`, with no mention of bugs at all, so "no bug" and "flag ignored" look identical.
- Workaround: Bugzilla REST quicksearch on the test file name.
