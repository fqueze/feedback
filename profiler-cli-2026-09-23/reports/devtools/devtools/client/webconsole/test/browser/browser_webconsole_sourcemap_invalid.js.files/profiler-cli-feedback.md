# profiler-cli feedback (browser_webconsole_sourcemap_invalid.js)

## `marker info` / `thread markers --list` round times to the second

- Question: in which order did the worker's fetch-response, control runnable, body-consumption and microtask markers run, and how far apart?
- Command: `profiler-cli marker info m-2631 m-3573 m-3577 m-3580 m-2641 --session <id>`
- Expected: start times precise enough to order markers that are microseconds apart (e.g. `141.1821s`).
- Got: `Time: 2m21s - 2m21s (16.170μs)` for every one of them, and `t=2m21s` in `--list`, even when zoomed to a 230 ms range.
- Workaround: `--json` and reading `start`.

## `load` selects the WebExtensions main thread instead of the parent main thread

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/JfZ3g5iLQSKPQEAY7FHmOg/runs/0/artifacts/public/test_info/profile_browser_webconsole_sourcemap_invalid-2.js.json" --session <id>`
- Expected: t-0 (GeckoMain, Parent Process) selected, as it was for the other per-test profile I loaded.
- Got: `t-13 (GeckoMain, WebExtensions)` selected. The next `thread markers --category Test --search <test>` returned "No markers match", which reads like the test logged nothing.
- Workaround: `thread select t-0`.

## (review) Same second-rounding hit again; `--session` rejected before the subcommand

- Command: `profiler-cli --session <id> thread markers --list --limit 0`
- Expected: a global option accepted anywhere, like `--session` is documented for scripting.
- Got: `error: unknown option '--session'`. It works only after the subcommand.
- Also: `thread markers --list` again printed `t=2m21s` for ~30 worker markers within 5 ms; ordering them needed `marker info --json` per handle.
