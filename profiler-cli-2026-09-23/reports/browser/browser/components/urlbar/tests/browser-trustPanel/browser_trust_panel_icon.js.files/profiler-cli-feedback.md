## Which markers have a stack through a given JS function?

- Question: which of the ~50 `SetNeedStyleFlush` markers in a 75 ms window were caused by `browser-trustPanel.js` code.
- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session <s>`, then a shell loop of `profiler-cli marker stack m-N` over 51 handles, grepping each stack.
- Expected: a filter such as `--stack-search browser-trustPanel.js` on `thread markers`, keeping only markers whose captured stack has a matching frame.
- Got: no such filter; 51 separate calls.

## Order of markers within the same millisecond

- Question: did a storage-completion runnable run before or after the test's `waitForCondition` check, both at t=29.171s?
- Command: `profiler-cli thread markers --list --limit 0 --session <s>` inside a 20 ms zoom.
- Expected: `--list` able to print sub-millisecond start times (e.g. `t=29.1716s`) when zoomed into a short range.
- Got: `t=` is rounded to 1 ms, so several markers show the same time. Workaround: `--json` and a Python script sorting `start`.

## Thread selected after `load` is not the parent main thread

- Command: `profiler-cli load <taskcluster url of profile_browser_trust_panel_icon-2.js.json> --session <s>`, then `thread markers --category Test --search ... --list`.
- Expected: the parent process GeckoMain selected by default, as with the first profile I loaded.
- Got: `t-65 (GeckoMain, WebExtensions)` was selected, and the query answered "No markers match the specified filters." Two calls lost before I noticed the header. Workaround: `thread select t-0`.
