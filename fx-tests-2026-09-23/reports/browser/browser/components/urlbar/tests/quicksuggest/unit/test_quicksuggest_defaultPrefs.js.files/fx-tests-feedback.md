## Question: which tests are behind this crash signature?
- Command: `fx-tests crashes --signature panic_hook --since 7`
- Expected: the tests behind the signature (the brief's "tests behind this crash signature").
- Got: one row, `279 crashes / 102 tests`, no test names; `--tests` exists on `failures` but not on `crashes`.
- Workaround: `--json`, which has `rows[].tests[]`. The text output could list the top tests, like `failures --tests` does.

## Question: what is this crash, beyond the Rust panic plumbing?
- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_defaultPrefs.js`
- Expected: one failure mode for one Glean panic (`No database found` on the glean.init thread).
- Got: three Issues rows for the same panic: `CRASH @ <mozglue_static::panic_hook ...` (3x), `CRASH @ MOZ_CrashSequence` (3x, same stack, other symbolication), and `FAIL Failure details not recorded (likely Android or platform logging issue)` (3x: ASan/TSan builds, where the sanitizer handles the SEGV and no minidump is written; the log has `Hit MOZ_CRASH(No database found)` plus the full sanitizer stack).
- Workaround: `fx-tests crash` on every dump, and reading the ASan/TSan log in the resource-usage profile.
- Would help: skip panic_hook/MOZ_Crash*/RustMozCrash frames when building a signature, show the crash reason (the Crash marker has `Reason: No database found`), and for sanitizer FAILs pick up the `Hit MOZ_CRASH(...)` / `ERROR: AddressSanitizer` line rather than "Failure details not recorded (likely Android...)", which is wrong for Linux ASan/TSan.

## `--bugs` does not say it found nothing
- Command: `fx-tests test <path> --bugs`
- Expected: a Bugs section, or "no bug names this test".
- Got: the normal summary again and no line about bugs, so "none found" cannot be told apart from "flag ignored".
