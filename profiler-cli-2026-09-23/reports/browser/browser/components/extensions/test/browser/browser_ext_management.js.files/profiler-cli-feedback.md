## Question: "which document is this refresh driver ticking for?"

- Command: `profiler-cli thread markers --search "4294967338" --list --session browser-browser_ext_management.js-1` (thread t-20, WebExtensions), also `--search "innerWindowID:4294967338,-name:RefreshDriverTick waiting for paint"`.
- Expected: the 8,652 `RefreshDriverTick waiting for paint` markers (and the `DocumentLoad` of that window), since `marker info` shows `innerWindowID: 4294967338` under "Fields (raw payload, no schema)".
- Got: `No markers match the specified filters.` / 0 markers. The bare-term search does not look at schema-less payload fields, nor at `innerWindowID` on markers whose schema has it (`DocumentLoad`).
- Workaround: `--json` over all `waiting for paint` and `DocumentLoad` markers, grouped by `data.innerWindowID` in Python, to map the ticking window to `moz-extension://…/popup.html`.
- What would have answered it: `--search innerWindowID:N` matching, or a `--group-by field:innerWindowID` that names each window's URL (from its `DocumentLoad` / `nsRefreshDriver initial timer start` marker).
