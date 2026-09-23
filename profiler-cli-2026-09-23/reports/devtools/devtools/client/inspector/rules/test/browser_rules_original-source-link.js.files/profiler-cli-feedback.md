## Ordering markers across threads needs --json (browser_rules_original-source-link.js)

- Question: in what order did the worker's `WorkerFetchResponseRunnable`, `ContinueConsumeBodyRunnable`, `Perform microtasks` and the main thread's `MainThreadFetchRunnable` / `Load` marker happen, within one second?
- Command: `profiler-cli thread markers --search "WorkerFetch,ContinueConsumeBody,Perform microtasks" --list --limit 0` and `profiler-cli marker info m-4065 m-5898 ...`
- Expected: timestamps precise enough to order markers a few ms apart (e.g. `t=5m43.170s`, or ms).
- Got: every row says `t=5m43s`; `marker info` says `Time: 5m43s - 5m43s (29.768ms)`. Within a busy second the list order is by start but the value is useless for cross-thread comparison.
- Workaround: `--json` and a python script printing `start`/`end` in ms. Also, Network marker payload fields (`startTime`, `responseEnd`, ...) are on a different time base (~10.8 ms offset here) from the marker's own `start`, with no hint of that in `marker info`.

## Which thread was active in a zoom window

- Question: which of 110 `DOM Worker` threads was the source-map worker active during the test (zoomed to 341.5–351 s)?
- Command: `profiler-cli zoom push 341.5,351` then `profiler-cli profile info --all`
- Expected: per-thread CPU within the zoom, or a way to list threads with samples/markers in the range.
- Got: whole-profile CPU per thread, ignoring the zoom.
- Workaround: `profiler-cli profile markers --search message` and read the "Matches by thread" block.

## `--session` before the subcommand is rejected with a misleading hint (review-browser_rules_original-source-link.js)

- Command: `profiler-cli --session review-browser_rules_original-source-link.js-1 thread markers --category Test ...`
- Expected: `--session` accepted as a global option, as `load ... --session` suggests.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session <id>` after the subcommand.
