## Question: "the failure messages of one test in one job"
- Command: `fx-tests task <taskId> --messages --full-messages`, then awk to cut out the block of one test (there is no `--test`/`--path` filter on `task`).
- Expected: a way to restrict `task` to one test path.

## Question: "was this crash an OOM, and was it address space or commit?"
- Command: `fx-tests crash <taskId> <dumpId>` shows signature and frames only; for `@ xul.dll + 0x074fe1a9` nothing says OOM.
- Workaround: curl `public/test_info/<dumpId>.extra` and read `MozCrashReason`, `OOMAllocationSize`, `AvailableVirtualMemory`, `SystemMemoryUsePercentage`, `UptimeTS`.
- What would have answered it: printing those .extra annotations in `fx-tests crash` (and the crash reason in `fx-tests test` Issues for unsymbolicated signatures).

## Question: "which of the harness-kill dumps is the parent process, and what was its main thread doing?"
- Command: `fx-tests crash WV24He4bR2KQDVcooD68UA.0 <dumpId>` for each of 10 dumps of a `application timed out after 370.0 seconds` hang: every one says "records no crashing thread".
- Workaround: `--all-threads --frames 25` on all 10 (7,500 lines), then pick the 82-thread one.
- What would have answered it: for a dump with no crashing thread, print the process type (or thread count) and thread #0's stack by default.

## `fx-tests test --since` takes a day count, not a date
- Command: `fx-tests test <path> --since 2026-09-10` -> "--since expects a non-negative integer". Minor; a date would be natural when comparing before/after a landing.
