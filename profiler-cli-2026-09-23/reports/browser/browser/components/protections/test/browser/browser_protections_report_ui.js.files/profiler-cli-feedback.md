## `thread markers --search` does not match the description column it prints

- Command: `profiler-cli thread markers --list --limit 0 --search "DOMContentLoaded - document" --session …` (reviewer, 2026-09-22)
- Expected: the `DOMEvent` row printed as `DOMContentLoaded - document`.
- Got: "No markers match the specified filters." The description is composed from `eventType` and target, so the text shown cannot be searched for as shown.
- Workaround: `--search DOMContentLoaded`, then pick the `DOMEvent` row by eye.
