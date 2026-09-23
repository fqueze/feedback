## Question: "exactly when, to the millisecond, did these markers start and end?"

- Command: `profiler-cli thread markers --session <s> --search "about:support,ShutdownLeakPathFinder,Completed ShutdownLeaks collections" --list --limit 0` (also after `zoom push 285.7,289.1`, a 3.4 s view), and `profiler-cli marker info m-175`.
- Expected: start times (and ends, for interval markers) precise enough to order events that are 0.1-0.3 s apart, at least once zoomed in to a few seconds.
- Got: `t=4m45s` on every row, whole seconds even in a 3.4 s zoom; `marker info` prints `Time: 4m45s - 5m2s (16.813s)`. That cannot tell whether a DOMWindow ended before or after a leak-check line 0.2 s away.
- Workaround: `--list --json` piped through a small script printing `start`/`end`/`label` (`timeline.py` in this directory). The default text output could show ms precision (e.g. `t=284.972s`), or at least scale precision to the view's duration, and `marker info` could print the end time with ms.
