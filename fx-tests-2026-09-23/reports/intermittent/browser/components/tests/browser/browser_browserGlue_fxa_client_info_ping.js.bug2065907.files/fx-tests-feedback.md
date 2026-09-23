## The minidump IDs of a hang (TIMEOUT then CRASH [Unknown])

- Question: which minidumps did the harness write when it killed the hung browser, so I can run `fx-tests crash` on them?
- Command: `fx-tests task dDDE4lzZQW-0HnM7tfIifg --profiles` (and `fx-tests test browser/components/tests/browser/browser_browserGlue_fxa_client_info_ping.js --task-ids --issue 2`)
- Expected: the minidump IDs next to the `CRASH [Unknown]` / `application timed out after 370.0 seconds with no output` entry, as `--task-ids` promises for crashes.
- Got: `CRASH, TIMEOUT — 4 failing executions of 4` and `[Unknown]`, no minidump ID; `--json` has no minidump field either. `fx-tests test` counts these runs as `0 crash`, so `--task-ids` lists none.
- Workaround: loaded the resource-usage profile in profiler-cli and read the `Minidump:` field of the `CRASH` markers (`marker info m-5`). Those dumps were the whole diagnosis (main thread deadlocked in Glean), so surfacing them on timeouts matters.
