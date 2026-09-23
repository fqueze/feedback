## The tests behind one crash signature

- Question: which tests does `@ mozalloc_handle_oom` crash in, and how many times each?
- Command: `fx-tests crashes --harness mochitest --signature mozalloc_handle_oom --limit 0`
- Expected: the per-test breakdown (the JSON has `rows[].tests[]` with 25 tests and counts).
- Got: one row, `225 crashes, 25 tests`, no test names.
- Workaround: `--json` and a Python one-liner over `rows[0].tests`.
- What would help: list the tests under each signature (at least with `--signature`, where there is one row).

## Why a failing test has no per-test profile

- Question: why does no per-test profile exist for this crash/timeout on Win32?
- Command: `fx-tests task JXS4Bg1qS0SfEBQ2MpLyQw --profiles` (also IdrIYaGbTdeFIRRINk38xg, X4JJ0Zx6Q8-5aoabu11H6A)
- Expected: when a failing test has no profile, the reason, if the log gives one.
- Got: "No failing test named a per-test profile in this job", or profiles only for other tests.
- Workaround: grep the full log for "upload profile": `TEST-FAIL | ... | failed to upload profile: [Exception... "Out of Memory" ... NS_ERROR_OUT_OF_MEMORY]` and `"Component is not available"`. On Win32 the long session is too short of address space to serialize its profile.
- What would help: print the `failed to upload profile: ...` line under the test, next to "profile".
