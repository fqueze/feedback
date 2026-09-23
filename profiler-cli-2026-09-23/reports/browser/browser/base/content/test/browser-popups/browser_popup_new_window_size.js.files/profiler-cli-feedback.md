## Marker times past 1 minute are rounded to the second

Question: in which order did the popup's windows get created/destroyed, and was that before or after the leak check, 3 minutes into a resource-usage profile?

- Command: `profiler-cli thread markers --session <s> --search "name:DOMWindow,name:DocShell" --list --limit 0` and `profiler-cli marker info m-274`
- Expected: start/end with ms precision (e.g. `t=187.496s … 206.156s`), as sub-second ordering is the whole question for leak/race analysis.
- Got: `t=3m7s` and `Time: 3m7s - 3m26s (18.660s)`; every marker in the test's 2 s window reads `3m7s`/`3m8s`.
- Workaround: `--json` and jq over `.flatMarkers[] | .start, .duration`, which cost several extra round-trips. A `--precise`/ms column, or keeping ms once past 60 s, would have answered it directly.

## `thread markers --list` has no end-time column for interval markers

Question: which windows (DOMWindow interval markers) were still alive at time T?
- Got: start and duration only, so each end time had to be computed via `--json` (`start + duration`).
- Would help: an `end` column, or a filter like `--alive-at <t>` for interval markers.
