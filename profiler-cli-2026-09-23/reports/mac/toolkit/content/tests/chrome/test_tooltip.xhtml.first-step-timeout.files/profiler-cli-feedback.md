## Question: the order and latency of events a few ms apart

- Question: in which order did the test's synthesized hover, a native mouse-enter and the next synthetic mouse move reach the document, within ~10 ms, and how long had each event been queued?
- Command: `profiler-cli thread markers --session <s> --search 'DOMEvent,-eventType:MozAfterPaint' --list --limit 0` (after `zoom push` to a 350 ms range)
- Got: every row's time printed as `t=7m47s`, so events 2 ms apart cannot be ordered or measured, and the DOMEvent `Latency` field, which is what separates a native event from a synthesized one here, is not in the row.
- Workaround: a script over `--list --json` printing `start` in ms with the `target` and `latency` fields (`ml.py` in this directory).
- What would have answered it: `--list` printing ms-resolution times when the view is short, plus the marker's key fields (latency for DOMEvent).

## Question (review): which handle is a link's `marker=N`?

- Question: when checking a report, which marker does each link's `marker=N` select?
- Command: `profiler-cli load '<profiler.firefox.com link with marker=2420117>' --session <s>`, then `profiler-cli status --session <s>`
- Got: the load opens the thread, but neither `load` nor `status` names the selected marker. Checking 30 links in 4 profiles meant a `thread markers --search` for each quoted marker, then `marker info <m-…> --json` to read `markerIndex` (script `review/mi.py`). The text output of `marker info` omits the index.
- What would have answered it: have `load` print the URL's selected marker as a handle, or have `marker info` accept an index, and show `markerIndex` in the text output.
