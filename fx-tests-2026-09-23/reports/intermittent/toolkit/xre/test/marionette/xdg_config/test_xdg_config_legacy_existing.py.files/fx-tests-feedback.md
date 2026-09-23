# fx-tests feedback — test_xdg_config_legacy_existing.py (bug 2005541)

## Question: which test failed first in each job annotated on this bug?

- Command: `fx-tests intermittent --bug 2005541 --since 60 --full-messages`
- Expected: the bug's own test (`test_xdg_config_legacy_existing.py`) among "Tests named", since it
  fails first in every one of these jobs.
- Got: "Tests named, per annotated job" and "Failure messages" list only the sibling tests'
  `TEST-UNEXPECTED-FAIL` lines (new.py 27x, new_env.py 18x, legacy_forced.py 8x, ...). The
  `TEST-UNEXPECTED-ERROR` of the bug's own test is not shown in any of the 72 jobs, and 17
  occurrences have an empty `lines` list (jobs where only ERRORs happened). Read as-is, it looks
  like sheriffs star other tests' failures on this bug and the named test never fails, which is
  the opposite of the truth.
- Workaround: script over `--json` `occurrenceRows[].taskId`, download each task's
  `public/test_info/marionette_errorsummary.log`, and list FAIL/ERROR per test in order. That
  showed the named test ERRORing first in 72/72 jobs.
- What the output could show: for each occurrence, the first failing test in the job's
  errorsummary, with ERROR as well as FAIL statuses.

## Question: what failed in this Marionette job?

- Command: `fx-tests task RgHJt3jJQbqZavx56fLmSw --profiles`
- Expected: the four failing xdg_config tests, in order.
- Got: "0 tests, 0 executions, 0 failing" plus a warning that 4 failing markers "named no test
  path" — they do name one, in Marionette's `<path> <Class>.<method>` form, which is not parsed.
- Workaround: read `marionette_errorsummary.log` and `live_backing.log` directly.

## Question: how often does this Marionette test fail, per config?

- Command: `fx-tests test toolkit/xre/test/marionette/xdg_config/test_xdg_config_legacy_existing.py`
- Got: exit 2, not in the xpcshell/mochitest data. Expected given the harness coverage, but no
  hint of where else to get a denominator.
- Workaround: Treeherder `/api/project/<repo>/jobs/?job_type_name=...` per Linux Mn job type
  to count runs (note: its `submit_timestamp__gt` filter was ignored; it returned the whole
  retention window, ~2026-05-25 onward).
