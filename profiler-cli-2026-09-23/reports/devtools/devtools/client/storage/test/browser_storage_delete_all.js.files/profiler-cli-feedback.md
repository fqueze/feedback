## Default thread after `load` is not the parent main thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/LAeUvrwYSKaPlCOKR49-2A/runs/0/artifacts/public/test_info/profile_browser_storage_delete_all.js.json --session browser_storage_delete_all.js-2`, then `profiler-cli thread markers --session browser_storage_delete_all.js-2 --category Test --search browser_storage_delete_all --list --limit 0`
- Expected: the test's log, as with the first profile loaded the same way (which auto-selected t-0 GeckoMain, Parent Process).
- Got: "0 markers (filtered from 25352)" on `t-81 (GeckoMain, Privileged Content)`, which `load` had selected. Nothing hinted that the Test markers live on another thread.
- Workaround: `thread select t-0` before every query.
- Could have shown: select the parent process main thread by default, or, when a filter matches nothing, say which thread has matches (as `profile markers --search` does).

## `marker stack` on a `Reflow (sync)` marker shows the cause stack, labelled "Captured at"

- Command: `profiler-cli marker stack m-912 --session browser_storage_delete_all.js-1`
- Expected: the stack at the time of the reflow, which was inside a `setTimeout() for exports.debounce…` Runnable marker.
- Got: `Captured at: 42.758s` followed by `selectTreeItem → set selectedItem → ensureSelectedVisible → Element.scrollIntoView`, i.e. the stack that dirtied layout earlier, not the one that flushed it. The neighbouring `DoFlushPendingNotifications` marker had the real flush stack (`#onLazyPanelResize → get hasScrollbar`).
- Workaround: read the stack of the `DoFlushPendingNotifications` marker instead.
- Could have shown: label it as the cause stack ("layout invalidated at"), as the profiler UI does.

## Question: "did request B start before request A ended?"

- Command: `profiler-cli thread markers --session browser_storage_delete_all.js-1 --search RDP --list --limit 0`, then `profiler-cli marker info m-413 m-299 m-297 --session browser_storage_delete_all.js-1`
- Both print times rounded to the millisecond (`t=42.758s`, `Time: 42.758s - 42.759s`). Ordering three RDP requests that started within 1 ms of each other needed `marker info … --json` and a script.
- Could have shown: start/end with 0.1 ms precision in `marker info`, or in `--list` when neighbouring markers share a rounded timestamp.

## Searching for a marker's label as printed finds nothing (review-browser_storage_delete_all.js)

- Command: `profiler-cli thread markers --search "resize - ,debounce,RDP Front" --list --limit 0 --session review-browser_storage_delete_all.js-2`
- Expected: the `DOMEvent  resize - window` rows that `--list` prints, searched by that printed text.
- Got: none of the DOMEvent rows. The printed label is assembled from `eventType` and `target`, so it is not a field that `--search` looks at.
- Workaround: `--search name:DOMEvent`, then filtering the output for `resize`.
- Could have shown: have `--search` also match the label `--list` prints, or say in `--help` that it does not.
