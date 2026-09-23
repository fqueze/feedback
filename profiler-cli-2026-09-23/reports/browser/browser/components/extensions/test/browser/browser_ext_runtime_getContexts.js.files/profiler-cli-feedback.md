# profiler-cli feedback (browser_ext_runtime_getContexts.js)

## `--search field:value` does not match a numeric payload field (innerWindowID)

- Command: `profiler-cli thread markers --session browser-getContexts-1 --search 'innerWindowID:4294967338'` (also bare `--search 4294967338`), on thread t-21.
- Expected: the 13,257 `RefreshDriverTick waiting for paint` markers, plus the `DocumentLoad` / `nsRefreshDriver` markers of that window.
- Got: `0 markers`. Yet `--group-by field:innerWindowID` on the same thread lists `4294967338: 13257 markers`, and `marker info` shows `innerWindowID: 4294967338` in the fields.
- Workaround: `marker info` on each candidate `DocumentLoad` marker to read its innerWindowID by hand.
- Question it was for: "which document does this stuck refresh driver belong to?"

## No way to list only the per-test `test` markers (the manifest timeline)

- Command: `thread markers --category Test --search 'name:test' --list`
- Expected: one row per test file (`PASS — browser/...` / `FAIL — browser/...`).
- Got: 6,123 markers, since `name:` is a substring match and also matches the `name` payload key of Text markers. `--search '— browser/'` matched none of them.
- Workaround: `--search 'browser_ext_c,browser_ext_b'` then filtering the output for rows named `test`.
- Question it was for: "which tests ran in this browser, in order, and which one failed first?" An exact-name match (e.g. `name=test`) would answer it.

## (review) `--session` is rejected before the subcommand

- Command: `profiler-cli --session browser-review-browser_ext_runtime_getContexts.js-1 thread list`
- Expected: the global option to be accepted anywhere, as with `git -C`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.
