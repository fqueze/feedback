## `profile markers --search` truncates silently

- Command: `profiler-cli profile markers --search test_select_input_change_event --session test_select_input_change_event.html-1`
- Expected: every matching marker, or a `… N more (--limit 0 for all)` line when the default limit cuts the list, as `thread markers` prints.
- Got: a header `40 markers across 2 of 17 threads` and exactly 40 rows, ending mid-test; the test's last 4 `TEST-UNEXPECTED-FAIL` markers (`... got 4, expected 6`, m-42..m-45) were missing, with no sign the list was cut. The header count reads as the total, not the number shown.
- Workaround: `thread select` the thread the markers were found on, then `thread markers --search ... --list --limit 0`.

## Test log markers of a mochitest-plain are not on the parent main thread

- Command: `profiler-cli thread markers --category Test --search test_select_input_change_event --list --limit 0` on the parent `GeckoMain` (t-0).
- Expected (from the diagnose brief): the test's `TEST-*` log.
- Got: `0 markers`. For mochitest-plain they are on the content process thread of the test page (`GeckoMain, http://mochi.test`), and in xorigin runs on `GeckoMain, http://mochi.xorigin-test`. Not a tool bug, but `thread markers` could hint that other threads have matches (as `profile markers` would show) when a search matches nothing on the selected thread.
- Workaround: `profile markers --search <test file>` to find the thread first.
