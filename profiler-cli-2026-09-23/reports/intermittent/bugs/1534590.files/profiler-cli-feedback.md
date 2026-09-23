## `zoom push` silently misreads the duration format that `thread markers` prints

- Command: `PROFILER_CLI_SESSION_OWNER=1534590 profiler-cli zoom push 15m30s,16m20s --session 1534590-2` (resource-usage profile of task KdpvmWnrTqa1S7nRCHXhvg, 22 min long)
- Expected: a zoom to 930 s - 980 s, since `thread markers --list` prints times in exactly this form (`t=15m35s`). Otherwise, an error saying the format is not accepted.
- Got: exit 0 and a zoom to about 15 s - 16 s (`View: ts-1→ts-2 (1s)`). The `m30s` / `m20s` parts were dropped without a word, and the next `thread markers` listed the wrong minute.
- Workaround: `zoom push 925,960` in plain seconds.

## `thread markers --search` does not match the label it prints (review-1534590)

- Question: "the `test` marker of the retry run of browser_selection.js".
- Command: `PROFILER_CLI_SESSION_OWNER=review-1534590 profiler-cli thread markers --session review-1534590-2 --search "PASS — browser/components/urlbar/tests/browser-newtab/browser_selection.js" --list` (resource-usage profile of KdpvmWnrTqa1S7nRCHXhvg)
- Expected: marker m-31, which the list prints as `test  t=16m  1.074s  PASS — browser/components/urlbar/tests/browser-newtab/browser_selection.js`.
- Got: "No markers match the specified filters". The search matches each field on its own, not the ` — `-joined label. There is also no option to filter by marker name alone, such as the `test` markers.
- Workaround: `--search browser_selection` and grep the output for `  test  `.
