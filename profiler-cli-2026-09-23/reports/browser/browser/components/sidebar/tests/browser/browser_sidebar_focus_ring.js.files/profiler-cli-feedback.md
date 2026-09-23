## Ordering events inside a 100 ms test (browser_sidebar_focus_ring.js)

- Command: `profiler-cli thread markers --category Test --search browser_sidebar_focus_ring --list --limit 0 --session S`
- Expected: timestamps precise enough to order markers inside a test that lasts ~100 ms (ms resolution).
- Got: every row shows `t=1m32s`; `marker info` also shows `Time: 1m32s`. Ordering the IO callback, the refresh tick and the assertions needed `--json` and a script over `.start`.
- Workaround: `--json | python3` printing `start`. Could show: `t=1m31.7307s` (or ms precision whenever the view/zoom is under a few seconds).

## Which style-dirtying markers came from one JS function

- Question: which `SetNeedStyleFlush` markers between two tests were caused by sidebar-main's IntersectionObserver callback.
- Command: none exists; `thread markers --search` matches name/payload, not stack frames.
- Got: had to list 1597 `SetNeedStyleFlush` handles and call `profiler-cli marker stack <h> --json` once per handle in a shell loop (~2 min).
- Could show: a `--stack-search <frame substring>` filter on `thread markers` (for markers with stacks).

## Which paint a screenshot shows

- Question: which main-thread paint (ViewManagerFlush "Transaction ID: N") a CompositorScreenshot shows.
- Command: `profiler-cli screenshots --range 91.60,91.80 -o dir`, then `marker info` on the screenshot and the Compositor's `ContentPaint Payload Presented` markers.
- Got: screenshot markers have no transaction id, and the GPU-side payload markers have none either, so matching them to main-thread transactions needed guessing from content.
- Could show: the transaction id (or the main-thread paint time) of the frame each screenshot captured.

## `marker info a..b --json` records have no handle

- Command: `profiler-cli marker info m-5099..m-5103 --session S --json`
- Expected: each record carries its `handle`.
- Got: `handle` absent (printed None), so the records had to be matched by position.
