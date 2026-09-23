## The tests behind one crash signature

Question: which tests does `@ mozalloc_handle_oom` crash in, and how many times each (to tell a
test-specific crash from a manifest-wide one).

- `fx-tests crashes --harness mochitest --signature mozalloc_handle_oom`
  - Expected: the tests behind the signature, with counts (it says "25 tests").
  - Got: one row, `225 crashes, 25 tests`, no test names.
  - Workaround: `--json`, then `.rows[].tests[]`. The per-test list was there; the default output
    could have printed it (or a `--tests` flag like `failures` has).
- `fx-tests failures --harness mochitest --message mozalloc_handle_oom --tests`
  - Expected: since `failures --tests` lists tests per message, that it would match the crash
    signature too.
  - Got: "No failure matched" — crashes are not in `failures`. Nothing in the output points to
    `crashes` for crash signatures; a hint there would have saved a call.
