## `fx-tests try` hides a test that failed every run, unless you pass `--limit 0`

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids`, then a grep for the test name.
- Expected: a test that failed 12 of 12 runs on two configs shows up.
- Got: nothing. It was cut off under PERMA-FAILS as `… 25 more (--limit 0 for all)`. A grep for the test name found nothing, which reads as "it did not fail here", and the brief treats that answer as `no longer fails`.
- Workaround: `--limit 0`.

## Question: which distinct tasks did this test fail in, on a try push?

- Command: `fx-tests try <rev> --task-ids --limit 0`.
- Got: `task RtbFHIe6SoKbSIGfJfQdfg.0` repeated 4 times (once per failing message and run), then `… 19 more tasks`. The 6 distinct jobs were not visible.
- Workaround: `--json`, plus a script that dedupes `taskIds` on (taskId, retryId).
- It could have shown one line per job, with its config and the number of failing executions in it.
