## Question: where were the hung processes stuck, across all the crash dumps of one test's failures?

- Command: `fx-tests test <path> --task-ids --limit 0` (73 `fx-tests crash <task> <dump>` lines), then `xargs -P 6` over `fx-tests crash <task> <dump> --all-threads --frames 0`, then rg/awk scripts to classify each dump's main thread and look for a frame (`_LSBundleFindWithNode`) on any thread.
- Expected: one command that aggregates a test's dumps: process type (here all "Firefox Nightly GPU Helper"), OS build, and the main thread's top in-Gecko frames grouped with counts, plus "dumps where frame X is on any thread".
- Got: one dump per invocation; the process name is only visible in the frames (`Firefox Nightly GPU Helper + 0x...`), not as a field.
- Workaround: fetched all 73 in parallel into files and grepped them.
- What would answer it: `fx-tests test <path> --dumps` (or `fx-tests crash --test <path>`) that groups the dumps by main-thread signature, with the process type and an optional `--frame <name>` count across all threads.

