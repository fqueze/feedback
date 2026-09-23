## `task --profiles` silently omits a per-test profile that failed to upload

- Command: `fx-tests task LOI2wKxuQFe84oGMG2nY0w --profiles` (also T5PHCq4fSnGtat0gIxvbUQ, bXxPXqRUTLaIrsMYMNhy3A, all win32)
- Question: where is this test's failure profile?
- Expected: a line saying no profile exists and why.
- Got: the failing test is listed with no `profile` line and "(+1 more message ...)"; only `--messages` reveals `failed to upload profile: [Exception... "Out of Memory" ...]`.
- Workaround: rerun with `--messages`. Worth saying "profile upload failed (out of memory)" in place of the profile line: every win32 failure of this test lacks a profile for this reason.
