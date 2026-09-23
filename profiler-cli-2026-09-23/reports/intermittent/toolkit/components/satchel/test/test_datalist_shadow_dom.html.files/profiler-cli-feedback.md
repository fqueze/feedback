## Test log of a plain mochitest is on the content thread, and a parent-thread search gives no hint

- Command: `profiler-cli thread select t-0` (parent GeckoMain), then
  `profiler-cli thread markers --category Test --search test_datalist_shadow_dom --list --limit 0 --session test_datalist_shadow_dom.html-1`
- Expected: the test's log (`TEST-PASS`, `TEST-UNEXPECTED-FAIL`, ...), or a pointer to where it is.
- Got: `0 markers (filtered from 143496)` / `No markers match the specified filters.` For
  mochitest-plain, the `TEST-*` markers are on the test page's content-process GeckoMain
  (`t-10`), and they carry the message, not the file name, so `--search <file name>` misses them
  on every thread; the `test` interval marker of the failing test itself is not in the per-test
  profile at all.
- Workaround: `profiler-cli profile markers --search "input value"` (a fragment of the failure
  message) found the thread; then `thread markers --category Test --list` on it.
- What would have helped: when a thread-scoped search matches nothing, say "N markers match on
  other threads: t-10 (...)", as `profile markers` already computes.
