## Daemon died silently mid-session

- Command sequence (session `browser_rules_completion-new-property_multiline.js-1`, per-test profile of task HlwX3bdxQoiyzI8eif8FaA, 1.86M markers on the parent main thread): `zoom push 161.940,161.990` → `thread samples --include-idle --limit 20` (answered "No samples in the current view") → `thread info` → `zoom clear` → `thread info`.
- Expected: answers.
- Got: `Session ... is not reachable. Nothing is accepting connections on ...sock. The daemon exited without cleaning up.` The daemon log ends with `Received message: command` and no error line, so there is no way to tell why it exited (OOM, crash, killed). Machine had 73 GB available.
- Workaround: reload the profile (several minutes), and all marker handles changed, so earlier `m-N` handles could not be linked.
- Suggestion: log the exit reason (uncaught exception, signal) to the daemon log before exiting.

## `thread samples` in a 50 ms zoom: "No samples in the current view"

- Command: `thread samples --include-idle --limit 20` after `zoom push 161.940,161.990` on the parent GeckoMain of the same profile.
- Expected: the ~5 samples a 10 ms interval gives, or a hint why there are none (e.g. the thread's sample range, or that samples were dropped for that time).
- Got: "No samples in the current view", with no hint whether that thread has samples at all near that time.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session review-multiline-1 thread markers --category Test --search ... --list --limit 0`
- Expected: the global-looking option accepted in any position, like `--json`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.

## (review) Question: in what order, to the ms, did the markers of a 40 ms sequence happen?

- Command: `thread markers --search "setTimeout callback,TimeoutExecutor" --list --limit 0` after `zoom push 161.945,161.984`; also `screenshots --range 161.94,162.3`.
- Expected: timestamps precise enough to order events within the zoom (e.g. ms, relative to the zoom or absolute).
- Got: every row says `t=2m42s`, so the five timer callbacks and 20 screenshots in the range cannot be ordered or placed against each other.
- Workaround: `--json` and a script over `flatMarkers[].start`; `marker info m-a..m-b --json` for the screenshots.

## (review) Question: which DOM changes in this range were made by function X?

- Command: `thread markers --search SetNeedStyleFlush --list` in a zoom, looking for the ones whose stack goes through `HTMLTooltip.js!show` or `autocomplete-popup.js`.
- Expected: a way to filter markers by a frame in their stack (like `--search` on samples), or the stack's top JS frames in the list row.
- Got: `--search` only matches name/category/payload, and the list row has no stack; 239 markers to open one by one.
- Workaround: `marker info <all 239 handles> --json` and a script over `stack.frames`.
