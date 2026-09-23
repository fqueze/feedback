# fx-tests feedback (no-bug.linux-debug-glean-panic-hook-crash)

## Question: "the tests behind this crash signature, and their configs"

- Command: `fx-tests crashes --signature 'panic_hook as core::ops::function::Fn<' --limit 0 --json`
- Expected: all 102 tests carrying the signature. Also some way to get their configs and task IDs, since the text output says "`fx-tests test <path> --task-ids` reads a bucket file, which does carry them".
- Got: `testCount: 102` but `tests` holds only 50 entries, even with `--limit 0`. The text output lists no tests at all. `jobNames` is empty.
- Workaround: a script (`collect.py`) runs `fx-tests test <path> --json` for each of the 50 tests to find the issue index whose message is the signature. It then runs `fx-tests test <path> --task-ids --issue <n> --json` and aggregates the job names. That took about 100 calls and still misses 52 tests.
- What could answer it: `crashes --signature X --tests`, listing every test and, from the bucket files, the per-config counts and task IDs.

## Question: "the panic message behind a generic panic_hook signature"

- Command: `fx-tests crash <task> <minidump> [--json]`
- Expected: the crash reason, "No database found". Every Rust panic shares the `panic_hook` signature, so the reason is the only discriminator.
- Got: signature, type, address and stack, but no `Mozilla crash reason` or `MozCrashReason` from the `.extra`.
- Workaround: grep the job's `live_backing.log` for `Mozilla crash reason:`.

## `fx-tests failures --message panic_hook` finds nothing

- Crash signatures are not "messages", so `failures` cannot group crashes. The "no match" answer reads as if nothing crashed. A hint pointing at `fx-tests crashes --signature` would help.
