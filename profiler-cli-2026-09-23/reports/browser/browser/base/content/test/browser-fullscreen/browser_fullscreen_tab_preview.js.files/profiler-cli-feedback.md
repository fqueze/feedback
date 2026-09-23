## Question: in what order did markers a fraction of a millisecond apart happen?

- Command: `profiler-cli thread markers --session <s> --search "nsWindow,mouse,pointer,TabHover" --list --limit 0` inside a `zoom push 4.2070,4.2115` (4.5 ms)
- Expected: timestamps precise enough to order a WM_MOUSELEAVE, the WM_NCHITTEST nested in it, and the DOM mouseout/TabHoverEnd it caused, all within ~1 ms.
- Got: `t=4.209s` / `t=4.210s` for all of them (ms resolution), so the causal order was not readable, even though the view was zoomed to a few ms.
- Workaround: `--json` and a python script printing `start` with 4 decimals of ms.
- What would help: scale the `t=` precision to the zoom width (e.g. µs when the view is under ~50 ms), or a `--precise` flag.
