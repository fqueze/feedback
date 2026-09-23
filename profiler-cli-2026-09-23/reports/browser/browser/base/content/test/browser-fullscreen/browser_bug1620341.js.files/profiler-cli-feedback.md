## Which markers have a stack through a given function

Question: which `SetNeedStyleFlush` markers on the parent main thread were caused by `_updateCloseButtons` (i.e. who set/removed the `closebuttons` attribute, and when)?

Command: `profiler-cli thread markers --session S --search SetNeedStyleFlush --list --limit 0` lists 282 markers with no stack text, and `--search` does not match stack frames. Workaround: dump `--list --json`, pass all 282 handles to `profiler-cli marker info <handles...>`, and grep the 7,600-line text output for the function name.

Expected: a way to filter markers by a frame in their captured stack (e.g. `--stack-search _updateCloseButtons`), or the `--list` rows showing the top JS frame of the marker's stack. That would have answered the question in one command.

## `marker info --json` records carry no handle

Command: `profiler-cli marker info m-825 m-375 ... --json`. Expected: each record to include its `m-N` handle, to pair times with handles. Got: no `handle` field (only name/start/end...), so the order of the arguments is the only way to match them up.

## (review) Which JS function caused each marker in a range — same need as the first entry

Question: in 3.9–4.32 s, which `SetNeedStyleFlush` came from `enterDomFullscreen` / `cleanupDomFullscreen` / `_updateCloseButtons`, next to the `RefreshDriverTick` and `requestAnimationFrame callbacks` rows. That meant repeating it on four profiles.

Command: `profiler-cli thread markers --session S --search "SetNeedStyleFlush,name:RefreshDriverTick,name:requestAnimationFrame callbacks" --list --limit 0`. It gives rows with no stack text. Workaround: `--json` handles → `marker info <all handles> --json` → a Python filter on the frame names (`review-seq.sh` in this directory).

Expected: `--list` rows could show the top 2–3 JS frames of each marker's stack. For `RefreshDriverTick`, that stack is the tick's cause, which would also have answered "who scheduled this tick" in one command.
