# fx-tests feedback — test_timer_flood.html

## Question: is there a bug for the leaking test?

- Command: `fx-tests test dom/base/test/test_suppressed_events_nested_iframe.html --bugs`
- Expected: a "Bugs naming this test" section, saying "none" if there is none.
- Got: the section is silently omitted, so the output is identical to running without `--bugs`;
  I could not tell "no bug" from "the lookup did not happen" until I ran the same flag on a test
  with a known bug (`test_timer_flood.html`, which prints `2055867`).
- Workaround: compare with a test known to have a bug.

## Question: which other tests fail in the same jobs as this one, across all its failing jobs?

- Command: `fx-tests task <taskId>` run in a loop over the 20 task IDs from
  `fx-tests test dom/base/test/test_timer_flood.html --task-ids --limit 0`, grepping the FAILED
  section.
- Expected: something like `fx-tests test <path> --co-failures` listing, per co-failing test, in
  how many of this test's failing jobs it also failed (here: test_suppressed_events_nested_iframe
  20/20, test_text-fragments-create-text-directive 20/20). That is the first thing to check for
  a victim.
- Workaround: the loop above (15 sequential `fx-tests task` calls).

## Question: why was a test not re-run?

- `fx-tests task dgAYedo7QCqEi55_NKj7Jw.0` says "CRASH, TIMEOUT — 2 failing executions of 2" for
  test_timer_flood.html, but the resource-usage profile has a single `test` marker for it; the
  "2 executions" are the TIMEOUT and CRASH records of one run, and the harness did not retry the
  crashed test. Saying "1 run (TIMEOUT then CRASH), not retried" would avoid reading it as a
  failed retry.
