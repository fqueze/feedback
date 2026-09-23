# profiler-cli feedback (browser_878452_drag_to_panel.js)

## Question: which tests ran in this browser session, in order, with their status?

- Command: `profiler-cli thread markers --category Test --search name:test --list --limit 0`
- Expected: only the per-test `test` interval markers (`PASS — <path>`).
- Got: every Test marker (TEST-PASS, INFO, ...), because `name` is also a payload field; the guide warns about it, but there is no way to select a marker by its own name only.
- Workaround: `--search "components/customizableui/test/browser_" --list --limit 0` into a file, then `rg '^\s+m-\d+\s+test\s'`.
- What would have answered it: a name-only filter (e.g. `--name test`), or a `profile tests` summary.

## Default session directory not writable

- Command: `profiler-cli load <taskcluster url> --session <id>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'` (sandboxed agent); the error message does say what to do.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call, including `profile-link.py`.

## Question: does this link's `marker=N` point at the marker the report quotes? (review-browser_878452_drag_to_panel.js)

- Command: `profiler-cli marker info m-13 --session <id>`
- Expected: the marker's `markerIndex`, to compare with a profiler URL's `marker=N`.
- Got: the text output has time, fields and innerWindowID, but not the index. Only `--json` has `markerIndex`, so I needed one `--json` call plus a script for each of the 22 cited markers.
- What would have answered it: `markerIndex` in the default `marker info` output (and in `thread markers --list` rows), or a `marker find --index N` that returns the handle.
- Also hit again: the default session directory is not writable in the sandbox (already logged above); `PROFILER_CLI_SESSION_DIR` works around it.
