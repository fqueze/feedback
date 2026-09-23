## Question: how many tests timed out after a given moment, and when did they start (resource-usage profile)

- Command: `profiler-cli thread markers --session test_nodelistactor.js-1 --search "name:test" --list --limit 0 --json`, then a Python script over `flatMarkers` to split `test` markers by `data.status` and by `start` relative to the first launch failure (t=131.676s).
- Expected: a way to get "count of test markers by status, started after t" from the default output, e.g. `--group-by field:status` combined with a `zoom push 131.676,end` that counts markers *starting* in the range (zoom currently also includes markers that merely overlap it, so 45 s TIMEOUT markers started before the break are included).
- Got: only the flat list (1113 rows) or aggregated-by-name stats; no per-field-value count restricted to start time.
- Workaround: JSON + script.

## Question: what did one test's replayed "full log" contain

- Command: `profiler-cli zoom push 178.300,178.302` then `thread markers --list --limit 0 --json`, slicing by index between `>>>>>>> Begin of full log for <test>` and `<<<<<<< End of full log for <test>`.
- Expected: the text list to be readable here; all the markers share one timestamp and logs of several tests interleave, so a plain `--list` in that zoom was 382 rows including long-lived `test` markers overlapping the range (69 KB).
- Workaround: JSON + slicing around the Begin/End handles. An option to list only instant markers, or only markers starting in the zoom range, would have answered it.
