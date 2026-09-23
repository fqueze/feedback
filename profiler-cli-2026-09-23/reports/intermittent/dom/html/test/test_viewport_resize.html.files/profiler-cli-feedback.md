## Question: "in what order did these markers happen within one millisecond, and which document (iframe vs. top page) does each belong to?"

- Command: `profiler-cli thread markers --search "-name:Preference Read,-name:Runnable,..." --list --limit 0 --session test_viewport_resize.html-2`
- Expected: a way to see sub-millisecond start times and the marker's `innerWindowID` (or the document URL it maps to) in the list, since a test page and its srcdoc iframe share one main thread.
- Got: `t=17.954s` for ~60 consecutive markers (image load, reflow, load event, script execution all in the same ms, some listed out of start order), and no document attribution. Deciding whether the iframe's `Image Load data:,a` came before or after the parent's first `Reflow` needed both.
- Workaround: `--json`, then a Python script sorting `flatMarkers` by `start` and printing `data.innerWindowID`. A `--precise-times` (or auto-precision when many markers share a ms) and an optional `window`/`doc` column mapping innerWindowID to the page URL would have answered it directly.

## Question: "which markers mention X" where X also appears in pref names

- Command: `profiler-cli thread markers --search "Image Load,DOMEvent,Reflow,DoFlushPendingNotifications,AsyncFrameInit,FinishDOMContentLoaded,ParseSheet,Script,..." --list --limit 0 --json`
- Expected: mostly markers of those names.
- Got: thousands of `Preference Read` markers whose payload contains "script" (e.g. `dom.script_loader.*`), 71 KB of output. Documented behaviour (bare terms match payloads), but `name:` is not name-only either for Text markers, so there is no clean "name is one of" filter.
- Workaround: filtered by `name` in Python over `--json`.

## review-test_viewport_resize.html: `marker info --json` gives `start` and `context.currentViewRange` in different time bases

- Command: `profiler-cli zoom push 17.945,17.956`, then `profiler-cli marker info m-1 --json`.
- Expected: `context.currentViewRange` in the same base as the marker's `start` (17951.93 ms, shown as `t=17.952s` in lists, and inside the zoom).
- Got: `currentViewRange.start` 17952.49, which is the zoom start plus `rootRange.start` (7.49 ms). The marker looked like it was outside the view it was listed in.
- Workaround: ignored `currentViewRange`. A narrow zoom showed that the zoom itself uses the same base as `start`.
