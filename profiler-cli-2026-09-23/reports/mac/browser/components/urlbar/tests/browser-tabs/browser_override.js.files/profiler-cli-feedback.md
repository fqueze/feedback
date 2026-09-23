## Which marker in a range has function X in its stack (browser_override.js)

- Question: "which of these 9 `SetNeedStyleFlush` markers, at 25.766-25.768 s, was dirtied from `unsplitTabs`?", and the same for `_endRemoveTab` inside a timer callback.
- Command: `zoom push 25.766,25.768`, then `thread markers --search SetNeedStyleFlush --list --json | <python: handles>`, then a shell loop of `marker stack <h> | rg -q unsplitTabs`.
- What the output could have shown: `thread markers` has no way to filter on the marker's stack, and `--list` shows no frame of it. Either a `--stack-search <fn>` filter, or the innermost JS frame printed in the `--list` row (for example `SetNeedStyleFlush [insertBefore ← unsplitTabs]`), would have answered it in one command.


## Which handle is the report's `marker=N` (review of browser_override.js)

- Question: "which marker handle has index 340747 (or 365887, 381017, 364173)?", to check a report's links. The linked marker was one of several `SetNeedStyleFlush` markers at the same millisecond.
- Command: `zoom push <t>,<t+2ms>`, `thread markers --search SetNeedStyleFlush --list`, then a shell loop of `marker info <h> --json | python3 -c '...markerIndex'` over each handle.
- What the output could have shown: `--list` rows do not include the marker index, and there is no `marker info --index N`. Either one would have answered it in one command. Same need as the entry above: the script was only there to reach a field the listing hides.

## Loading a raw Taskcluster URL selects a content thread (review of browser_override.js)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/P5hlNB7QRCqTpwfGXWQH7w/runs/0/artifacts/public/test_info/profile_browser_override.js.json --session <s>`
- Expected: the parent process main thread selected, as when loading the profiler link of the same profile (`thread=0`).
- Got: `t-39 (GeckoMain, Privileged Content)`, so the next `thread markers` returned "No markers match".
- Workaround: `thread select t-0`.
