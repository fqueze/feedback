## Log markers (Test category `INFO`) show `Message: (empty)` although the payload has the text

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` and `profiler-cli marker info m-285 --session <s>` on the per-test profile of task Qhag1V4PSkmuob-XcJocRw (profile_test_bug1100912.html.json).
- Expected: the INFO line's text, e.g. `must wait for focus`, in the list row and in `Message:`.
- Got: `INFO  [(empty)] INFO: (empty)` in the list, `Level: (empty)` / `Message: (empty)` in marker info. `--json` shows `"value": "must wait for focus", "formattedValue": "(empty)"` — the formatter drops the value (Log marker type with `level`/`message` fields).
- Workaround: `marker info --json` per marker and read `fields[].value`.
- Question it blocked: "what did the test log with info() before it hung" — the test's own log, the first thing the brief says to read.

## Mean CPU of the machine over a range needed a script

- Command: `profiler-cli thread markers --search 'name:CPU Use' --list --limit 0 --json` on a resource-usage profile, zoomed to the 300 s of a timeout, then a Python mean over `data.cpuPercent`.
- Question: "was the machine saturated while the test timed out?" (the brief's slow-machine check).
- What the output could have shown: a min/mean/max of the CPU Use payload in the aggregated `thread markers` view (it only shows durations), or a `counter` for it.

