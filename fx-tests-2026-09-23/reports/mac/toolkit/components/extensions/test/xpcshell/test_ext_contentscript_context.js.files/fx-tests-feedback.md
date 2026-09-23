# fx-tests feedback — test_ext_contentscript_context.js

## Question: which mechanism is behind this test's timeouts?

- Command: `fx-tests test <path> --task-ids --issue 2 --limit 0`
- Expected: some way to split "Test exceeded time limit" by what happened inside the run.
- Got: 843 task IDs under one row. Most of them are a content process hitting a fatal
  `###!!! ASSERTION: Overwriting an existing document channel!` (bug 2043133), and the rest are a
  plain slow run, but nothing in the output separates them.
- Workaround: downloaded 23 `live_backing.log`s and grepped each one's replayed full log for the
  assertion and the last `Starting <subtest>` line (about 20 MB per log).
- What the output could show: for a TIMEOUT, the first `ASSERTION` / `Hit MOZ_CRASH` line from the
  replayed output, and the last subtest started. Either would split these timeouts into their two
  mechanisms.

## `errors --test` misses an occurrence on a date it covers

- Command: `fx-tests errors --harness xpcshell --day 2026-09-20 --test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_context.js --kind "C++ assertion" --task-ids`
- Expected: the assertion from task COszRY6cQ3eYMfxkW91UAg (ran 2026-09-20 01:07 UTC; its log has
  the assertion in the replayed full log).
- Got: one occurrence, from Qn5h1mHIQOKAmxlUtWOq0g only. On 2026-09-18, with 68 timeouts of this
  test, it returned "No markers matched", but my log sample shows the assertion in most debug
  timeouts that week.
- So the errors file seems to cover only some of a day's jobs, and the output does not say so. It
  says "386 jobs" but not how many were missed.

## Question: how long does this test take when it passes in the parallel phase?

- Command: `fx-tests test <path> --durations --limit 0`
- Got: test-macosx1500-aarch64-vms/debug-xpcshell median 2833 ms, p95 38151 ms. The median is the
  sequential harness retries after failures (about 2.1-2.7 s each in the logs), not the parallel runs.
- What the output could show: durations split by parallel vs sequential execution, since the
  verdict line already knows the mode.
