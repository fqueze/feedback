## Ordering markers that fall within the same millisecond (test_async_transactions.js)

- Question: in which order did a PutEvent, a TaskController::AddTask and a Runnable happen, all
  printed as `t=1.733s` / `t=1.734s`?
- Command: `profiler-cli thread markers --session test_async_transactions.js-1 --search "-name:AsyncShutdown blocker" --list --limit 0`
- Expected: enough precision to order them once the view is zoomed to 17 ms
  (`zoom push 1.7325,1.7495`).
- Got: times rounded to the ms (`t=1.733s`) past 1 s, so five markers share a timestamp.
- Workaround: `--json` piped to a Python script printing `start` (e.g. 1733.0382 vs 1733.5098).
  The text list could print times with precision relative to the zoomed range width.

## Machine CPU and concurrently running tests at a moment of a per-test profile

- Question: was the machine saturated, and which tests were running, at t=1.734s of a per-test
  profile?
- Commands: `profiler-cli profile meta` on both profiles to subtract their `Started:` times by
  hand (614.983 s offset), then on the resource-usage profile
  `thread markers --search "name:CPU Use" --list --json` and `thread markers --search "name:test" --list --json`
  through scripts to print `cpuPercent` / idle per marker and the `test` markers overlapping one
  instant.
- Expected: `--list` for CPU Use markers showing their cpuPercent/idle fields in the text row,
  and a way to ask for the markers overlapping one time point (e.g. `--at <t>`).
- Got: the text row for `CPU Use` is just `CPU Use`, with no field values; no "overlapping t"
  filter, so zooming to a range lists every marker starting in it but not the long ones that
  started earlier (the test markers did show since zoom keeps overlapping ones, but grepping 5000
  of them needed a script).

## Counting Runnable markers by runnable name (review-test_async_transactions.js)

- Question: within a zoomed range, how many `AsyncExecuteStatements::notifyResultsOnCallingThread`
  vs `notifyCompleteOnCallingThread` runnables ran?
- Command: `profiler-cli thread markers --session review-test_async_transactions.js-1 --search "AsyncExecuteStatements" --limit 0`
  (after `zoom push 1.749,10.2`).
- Expected: the aggregate split by runnable name, since that is what the search matched.
- Got: `Runnable 50 markers`, one group; the runnable name is only in the label. `--list` would
  print 50 rows to count by eye.
- Workaround: `--list --json` piped to a Python `Counter` over `label`. `--group-by` might do it,
  but the aggregate output did not suggest a key; grouping Runnable markers by their name field
  by default (or hinting `--group-by field:name`) would answer it.
