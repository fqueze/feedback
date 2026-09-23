## `thread markers --list` times are rounded to the second on long profiles

- Command: `profiler-cli thread markers --session <s> --search "about:support,ShutdownLeakPathFinder" --list --limit 0` on a 20-minute resource-usage profile.
- Expected: sub-second start times (and end times for interval markers), since ordering events 100-500 ms apart (window destroyed vs. leak-check start) is the whole question.
- Got: `t=19m37s`, `t=19m41s` etc. — every marker in the same second prints the same time, so the order of a DOMWindow's end vs. the `ShutdownLeakPathFinder | Capturing CC graph` marker could not be read from the list; `marker info` gives duration but also only `19m37s - 19m53s`.
- Workaround: read the timestamps from the task's live_backing.log instead.
