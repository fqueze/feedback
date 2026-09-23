# fx-tests feedback (browser_smartwindow_manage_tabs_tool.js.win32-crash)

## Question: which tests are behind this crash signature?
- Command: `fx-tests crashes --harness mochitest --signature mozalloc_handle_oom`
- Expected: the tests that crashed with it, with their counts (the answer to "is this OOM specific to my test or does it land on whichever test is running").
- Got: one row, `225  25  0  @ mozalloc_handle_oom` — the count of tests but not which ones.
- Workaround: `--json` and read `rows[].tests[]`. The default output could list the top tests under each signature (it has them).

## Question: how much address space / memory was left when this OOM crash happened?
- Command: `fx-tests crash <task> <minidump>` (and `--json`)
- Expected: for an OOM signature, the crash annotations that answer it: `MozCrashReason`, `OOMAllocationSize`, `AvailableVirtualMemory` / `TotalVirtualMemory`, `SystemMemoryUsePercentage`, `ProcessType`, `UptimeTS`.
- Got: signature, type, address, crashing thread and stack only. "32-bit address space exhausted" vs "machine out of memory" cannot be told apart from it.
- Workaround: curl `public/test_info/<minidumpId>.extra` for each of the 88 crashes and summarize with a script.

## Question: was the address space full, or fragmented?
- Command: `fx-tests crash <task> <minidump>`
- Expected: for a 32-bit OOM, a summary of the dump's MemoryInfoListStream (free total, largest free block, reserved/committed by type), as Socorro does ("largest free VM block").
- Got: nothing about the memory map; the processed `.json` artifact has none either.
- Workaround: downloaded 7 `.dmp` files and parsed stream 16 with a 40-line Python script. Note that 32-bit Windows addresses above 2 GiB come sign-extended in that stream, and that the largest free block in every dump is the crash reporter's 80 MiB reservation released just before the dump — worth subtracting in any summary.

## Question: which crashing threads and crash reasons make up the unsymbolized `xul.dll + 0x…` signatures?
- Command: `fx-tests test <path> --config windows11-32 --task-ids --limit 0`
- Expected: the crash reason (MozCrashReason) next to each crash, since most signatures here are unsymbolized and each appears once, so the Issues list (51 rows) hides that they are all OOMs.
- Got: one row per unsymbolized address.
- Workaround: `fx-tests crash --json` for all 88 minidumps plus the `.extra` files, grouped by reason and thread.
