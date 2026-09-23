## Question: "which markers were emitted from inside function X" (stack search on markers)

- Command: `profiler-cli thread markers --session S --search SetNeedStyleFlush --list --limit 0 --json`, then a shell loop running `profiler-cli marker stack m-N` on each of ~230 markers and grepping for `adjustHeight` / `_invalidate`.
- Wanted: the times at which `autocomplete-popup.js!adjustHeight` ran (it only shows up as the stack of `SetNeedStyleFlush` markers, with no sample landing in it at 10 ms intervals).
- Got: `--search` matches only name/category/payload, never the marker's stack, so the only way was ~230 separate `marker stack` calls (several minutes, per profile, three profiles).
- What would have answered it: a `--stack-search <func>` (or `--has-stack-frame`) filter on `thread markers`, or a `--with-stack-top N` option on `--list` that prints the first N JS frames of each marker's stack.


## Review: searching for a DOMEvent by the text the list prints finds nothing (browser-review)

- Command: `profiler-cli thread markers --session S --list --limit 0 --search "underflow,overflow,flow - richlistbox,pointerout - panel"`
- Expected: the rows printed as `underflow - richlistbox@… class="…"` and `pointerout - panel@… id="PopupAutoComplete"`.
- Got: the bare terms matched; `flow - richlistbox` and `pointerout - panel` silently matched nothing, although that is the exact text the list shows (it is built from the `eventType` and `target` fields, which are searched separately).
- Workaround: search one field's word (`pointerout`), then grep the output.

## Review: loading the raw Taskcluster URL of a per-test profile selects a WebExtensions thread (browser-review)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/YqPufYJDTfezafaahAyJ2Q/runs/0/artifacts/public/test_info/profile_browser_autocomplete_secondary_action.js.json --session S`, then `thread markers --search …`
- Expected: the parent process main thread selected, as when loading the profiler link.
- Got: `t-32 (GeckoMain, WebExtensions)` selected. The next marker searches printed only "No markers match the specified filters." with no thread name in that line, so it read as "the marker is not there".
- Workaround: `profiler-cli status`, then `thread select t-0`.
