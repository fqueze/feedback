## `marker info --json` on a CompositorScreenshot embeds the whole image (review-browser_localSearchShortcuts_xul.js, 2026-09-22)

- Command: `profiler-cli marker info m-180 --json --session <s>` (m-180 a CompositorScreenshot), to read `markerIndex`.
- Expected: the marker's metadata; `rawFields` already truncates the data URL.
- Got: a top-level `screenshot.url` with the full base64 JPEG (~20 KB of text), dumped into the agent's context.
- Workaround: pipe through a script dropping `screenshot`/`rawFields`. The question was "which marker index does this handle have": a `--fields` option, or truncating `screenshot.url` like `rawFields` unless `--full`, would answer it.
