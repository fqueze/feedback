## Question: "which of these two markers came first?" — the default output rounds start times to 1 ms

- Command: `profiler-cli thread markers --search "DOMEvent,Reflow,Test,…" --list --limit 0 --session …`
- Expected: enough precision to order markers that are close together. Here that was a console error at 9955.64 ms and the RefreshDriverTick that ran the pending rAF at 9956.24 ms. The whole diagnosis hinged on that order.
- Got: both printed as `t=9.956s`, and `marker info` also prints `Time: 9.956s`.
- Workaround: `marker info <m> --json` and a python one-liner to read the `start` field of each marker.
- What would have answered it: sub-ms start times in `--list` when neighbouring rows share the same rounded ms, or a `--precise`/`--us` flag.
