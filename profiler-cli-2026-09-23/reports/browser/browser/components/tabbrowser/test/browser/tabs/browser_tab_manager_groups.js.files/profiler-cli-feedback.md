## Question: which markers started inside this short window? (browser_tab_manager_groups.js)

- Command: `profiler-cli zoom push 6.8785,6.9075 --session S; profiler-cli thread markers --session S --list --limit 0`
- Expected: the markers that happened in those 29 ms (what woke the main thread after an idle gap).
- Got: the list starts with ~100 long-running markers that merely overlap the window (IPCIn/IPCOut pairs lasting 5-16 s, `CSS animation` lasting 6-20 s), pushing the relevant rows far down; `--category Other` still mixes in hundreds of `image-drawing`/`AddScriptRunner` instants.
- Workaround: `--category Other --list --limit 0 | awk '$3 ~ /^t=6\.(879|88|89|90)/'` plus `rg -v` for noise.
- Could have shown: a `--starting-in-range` (or `--max-duration`-style) flag that keeps only markers whose start is inside the committed zoom range.

## `thread list` CPU column ignores the zoom

- Command: `profiler-cli zoom push 6.85,6.91 --session S; profiler-cli thread list --session S`
- Expected: per-thread CPU within the zoomed 60 ms, to see which thread was busy while the main thread was idle.
- Got: whole-profile CPU totals, with the zoom shown in the header.
- Workaround: `thread select t-1` / `t-3` then `thread markers --min-duration 2 --list` filtered by start time.
