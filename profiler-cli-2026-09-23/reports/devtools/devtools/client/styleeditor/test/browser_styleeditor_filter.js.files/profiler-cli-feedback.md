## Test log markers carrying a data: URL make `--list` output unreadable (browser_styleeditor_filter.js, 2026-09-22)

- Command: `profiler-cli thread markers --category Test --search browser_styleeditor_filter.js --list --limit 0 --session ...`
- Expected: one line per marker, readable.
- Got: 37.8 KB of output for 303 markers. The "Adding a new tab with URL" / "Navigating to" INFO markers hold a several-KB data: URL each, printed in full.
- Workaround: `| cut -c1-300`. A per-line width cap (like fx-tests' COLUMNS) would avoid this.

## Question: "which JS function set off each of these markers?" (browser_styleeditor_filter.js, 2026-09-22)

- Commands: `thread markers --search name:SetNeedStyleFlush --list`, then a shell loop of `profiler-cli marker stack m-N` over 25 to 50 handles, keeping the first 4 frames.
- The list view shows `✓` (has stack) but no frame at all, so finding the one `setActiveSummary` / `#clear` among dozens of SetNeedStyleFlush markers takes one call per marker. Showing the first non-native JS frame (or a `--stack-frames N` option) in `--list` would answer this in one call.

## Question: "when was function X on the stack?" (browser_styleeditor_filter.js, 2026-09-22)

- Commands: `thread functions --search styleeditor` repeated under five `zoom push` windows to bisect when StyleEditorUI functions were sampled.
- `thread functions` / `thread samples` give counts but never sample times. The first and last sample time per function (or a `--list` of sample times for a `--search`) would have answered this directly.

## A `load` that fails leaves its session registered but dead (review-browser_styleeditor_filter.js, 2026-09-22)

- Command: `profiler-cli load '<profiler.firefox.com from-url link>' --session review-browser_styleeditor_filter.js-1`
- Expected: on a failed fetch, no session left behind, so re-running the same `load` retries.
- Got: `Profile load failed: ... 503 Service Unavailable` (a transient Taskcluster error; `curl` got a 200 right after). Re-running `load` then said `Session ... is already running`, and `status` / `profile info` on it just repeated the 503 error.
- Workaround: `profiler-cli stop <id>`, then `load` again. Either clean up the session on a failed load, or retry transient 5xx responses.
