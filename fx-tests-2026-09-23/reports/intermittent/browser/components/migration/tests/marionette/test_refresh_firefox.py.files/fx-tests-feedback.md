# fx-tests feedback (test_refresh_firefox.py, bug 1916861)

## `fx-tests test` knows nothing about marionette tests

- Command: `fx-tests test browser/components/migration/tests/marionette/test_refresh_firefox.py`
- Expected: rates per config, history, failing task ids (the harness is marionette).
- Got: `No test path in the xpcshell and mochitest 21-day data contains "..."`. `--harness` only
  accepts xpcshell|mochitest, so no rate, `--history` or `--task-ids` exists for marionette tests.
- Workaround: Treeherder `/api/project/<repo>/jobs/?job_type_name=...` for every job, plus each
  task's `public/test_info/marionette_errorsummary.log`, fetched by a script.

## `fx-tests task` says "no tests at all" when the resource-usage profile has 254 `test` markers

- Command: `fx-tests task eYq3nlTIQoK7PwfeZlWvbQ --profiles`
- Expected: the job's tests with their statuses. The resource-usage profile has a `test` marker per
  marionette subtest, e.g. `ERROR — browser/components/migration/tests/marionette/test_refresh_firefox.py TestFirefoxRefresh.testFxANoSync`.
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all", plus a
  warning that 4 failing markers "named no test path". They do name one, followed by
  ` Class.method`, which is how marionette names a test.
- Workaround: loaded the profile in profiler-cli and ran `thread markers --search <file>`.

## Question: "what failed in each job annotated on this bug" (`intermittent --bug`)

- Command: `fx-tests intermittent --bug 1916861`
- Got: "Failure messages, per annotated job" and "Tests named" cover 12 of the 80 annotated jobs.
  The other 68 jobs, the dominant failure (all 4 subtests `TEST-UNEXPECTED-ERROR`
  `PermissionError ... Desktop/Old Firefox Data`), get no line at all, so that mode could not be
  seen from the output.
- Workaround: a script that downloaded all 80 `marionette_errorsummary.log` files and grouped them
  by message.
- What the output could show: a message for each job, `ERROR` status included, plus a count of
  jobs that have no message.

## Question: "which worker machines do the failing jobs run on, compared with passing jobs"

- There is no fx-tests command for this. Here the answer was the whole diagnosis: every run on
  one set of hosts fails, and no run on the other hosts fails.
- Workaround: Taskcluster `task/<id>/status` for the workerId of each failure, and the Treeherder
  jobs API (`machine_name`) for passing jobs of the same job types, joined by a script.
- What the output could show: under `intermittent --bug` or `test --task-ids`, failures per
  machine next to that machine's runs in the same window.
