## `zoom push` silently misparses the times the tool itself prints

- Command: `profiler-cli zoom push 20m39s,20m56s --session <s>` (times copied from `thread markers --list` output, which prints `t=20m39s`).
- Expected: a 17 s view from 1239 s to 1256 s, or an error saying the format is not accepted.
- Got: "Pushed view range: ts-1 (20s) to ts-2 (20.000s)" in effect (seen on `zoom pop`), i.e. `20m39s` read as 20 s and a 1 ns view; the following `thread markers --list` then listed long-lived markers overlapping t=20 s, which looked like a plausible answer.
- Workaround: convert to seconds by hand (`zoom push 1239,1256`).
- Suggestion: accept the `XmYs` form the list output uses, or reject it with an error.

## Question: "which markers started inside this time range"

- Command: `profiler-cli zoom push 2893.30,2893.70` then `thread markers --search '-name:CPU Use,...' --list --limit 0`.
- Wanted: the DOMWindow/DocShell/log markers that began during a 400 ms toggle, to see which windows a click created.
- Got: every marker overlapping the range, led by session-long DOMWindow/DocShell intervals and the `run-tests`/`suite`/manifest markers (the first ~80 rows were things created 47 minutes earlier).
- Workaround: `--list --limit 0 --json` piped to a Python filter on `start`.
- What would have answered it: a `--starts-in-range` (or `--overlap=start`) option on `thread markers`.

## `marker info --json` records have no handle

- Command: `profiler-cli marker info m-469 m-470 m-432 --json`.
- Expected: each record to carry its `m-N` handle, so several records can be matched to the handles asked for.
- Got: `handle` absent (None); had to rely on order.

## Question: "how many GC/CC batches did this object survive, and what was freed with it" (review)

- Command: `profiler-cli thread markers --search name:DocShell --list --limit 0 --json`, then a Python script grouping parent DocShell markers by end time.
- Wanted: to see whether docShell 1105 became garbage only when browser_aiwindowui.js ran, meaning it survived every earlier free batch.
- Got: the list, sorted by start time only, with durations. End times have to be computed by hand, and nothing groups markers that end together.
- What would have answered it: an `--ends-in-range` filter or `--sort end` on `thread markers`.
