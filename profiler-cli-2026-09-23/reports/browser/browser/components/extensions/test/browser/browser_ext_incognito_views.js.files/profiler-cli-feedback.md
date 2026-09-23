
## Review (browser-review-browser_ext_incognito_views.js), 2026-09-22

Question: "which window/document is this marker for?" (which window a `deactivate - window` / `activate - window` DOMEvent targets; which document's refresh driver logs `RefreshDriverTick waiting for paint`).

- Command: `profiler-cli thread markers --search "innerWindowID:8589934682" --list --session …` and `--search "8589934682"`.
- Expected: the markers whose payload has that `innerWindowID` (shown by `marker info` under "Other payload fields (no schema)").
- Got: `No markers match the specified filters.` The `--list --json` `flatMarkers` entries do not carry `innerWindowID` either.
- Workaround: `marker info` on each candidate handle, one by one. The list could show the inner window ID (or the window/document URL it maps to) as a column, and `--search` could match it.
