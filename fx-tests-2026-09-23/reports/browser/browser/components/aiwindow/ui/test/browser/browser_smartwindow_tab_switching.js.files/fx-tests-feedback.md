## Question: which tests are behind this crash signature?

- Command: `fx-tests crashes --harness mochitest --signature mozalloc_handle_oom --limit 0`
- Expected: the tests hitting the signature (the row says `tests: 25`), at least the top few with counts.
- Got: one row, `225 crashes, 25 tests, @ mozalloc_handle_oom`, with no test names. The names are in `--json` (`rows[].tests[]`), not in the default output.
- Workaround: `--json | head`. The default output could list the top N tests per signature, as it already knows them.

## Question: how many times did this test run in this job, and how many failed?

- Command: `fx-tests task HDcNU1rdTbWH5lcCdAZOoA.0 --profiles` (same in fMZcf0_7R0C1YX_0UfiMqA.0, OIKhQkNvROmPApZWHm2ijw.0, LQR4UsHYQpKbbBzkrJEy3Q.0)
- Expected: `CRASH, TIMEOUT — 1 failing execution of 2` (the first run crashed, the harness retry passed).
- Got: `CRASH, TIMEOUT — 11 failing executions of 12`. The resource-usage profile has one TIMEOUT `test` marker for the first run, a `processing 10 crashes` marker (10 dumps: parent plus content processes), and one PASS for the retry. The 10 dumps seem to be counted as 10 executions.
- Workaround: read the `test` markers in the resource-usage profile.

## Question: is this unsymbolicated main-thread crash an OOM?

- Command: `fx-tests crash YEu1QA0vTtGerviKaWQRNQ.0 79688cf5-2817-47bf-801e-4587da763b49` (and `--json`)
- Expected: the `.extra` crash annotations, at least `MozCrashReason` (e.g. `MOZ_CRASH(OOM)`), `AvailableVirtualMemory`, `OOMAllocationSize`. With `xul.dll + 0x…` frames only, these are the only way to tell an OOM abort from another crash.
- Got: signature, `EXCEPTION_BREAKPOINT`, address, unsymbolicated frames. No crash reason in the text or the JSON.
- Workaround: none used; the 7 `xul.dll + 0x…` crashes of this test are left as "consistent with OOM" in the report.
