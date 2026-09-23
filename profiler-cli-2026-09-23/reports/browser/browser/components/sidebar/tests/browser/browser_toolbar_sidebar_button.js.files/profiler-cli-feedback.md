## Question: which markers in a range have a stack going through function X?

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --json` (to find the `SetNeedStyleFlush` markers set by `sidebar-main.mjs!createToolsObservers/this._toolsIntersectionObserver<`).
- Expected: a way to filter markers by a frame in their stack (e.g. `--stack-search createToolsObservers`), or stacks in the `--list --json` output.
- Got: the list has `hasStack: true` but no stack; `--search` only matches name/payload.
- Workaround: took handles from the JSON, ran `marker info` on 1,490 handles in chunks of 200 (42k lines of output to a file), then a Python script to split records and match the frame. Costly in time and disk; a stack filter would have answered it in one call.

## Minute-scale profiles print times without milliseconds

- Command: `profiler-cli marker info m-14 m-15 m-16 --session ...` and `thread markers --list` on a 2m6s profile.
- Expected: times precise enough to order events that are ms apart (e.g. `123.976s` or `2m3.976s`).
- Got: `Time: 2m4s` for all three markers, 200 ms apart; the list column also shows `t=2m4s` for every row, so the chronology inside one second is unreadable.
- Workaround: re-ran with `--json` and read `start`.

## Review: same question again — which markers in a range have a stack through function X?

- Command: `thread markers --search SetNeedStyleFlush --list --limit 0 --json`, then `marker info` over the handles in chunks of 200, then grep for `IntersectionCallback`; done three times (Linux before/after the click, macOS).
- Expected: `--stack-search <frame>` (or stacks in `--list --json`) answering it in one call.
- Got: `hasStack: true` only.
