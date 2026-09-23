## `fx-tests task` says a gtest job's profile "records no tests at all"

- Command: `fx-tests task BvDIGc2eT3mdfeux-2uS4w --profiles` (test-windows11-64-25h2-asan/opt-gtest-1proc)
- Expected: the gtest failures listed as tests (`cubeb.loopback_duplex` FAIL, ...), since the
  resource-usage profile holds 13,506 `test` markers with gtest names and a Status field.
- Got: "0 tests, 0 executions, 0 failing" and "This profile records no tests at all. Either the job
  is not a test job, or the harness died before it ran one" — only a warning line named the 14
  failing gtests, as "markers that named no test path".
- Workaround: loaded the profile with profiler-cli and searched the `test` markers by name.

## Question: how often does one gtest fail, over the window?

- Command: `fx-tests test cubeb.loopback_duplex` -> "No test path in the xpcshell and mochitest
  21-day data contains ...".
- The answer (runs per config, failures of this gtest per config, per day) had to be rebuilt from
  Treeherder `similar_jobs` (paged, ~5,000 jobs) plus `text_log_errors` for every failed job.
- What would have answered it: gtest names in `fx-tests test`, or at least `fx-tests intermittent`
  printing the job population (runs of that job type) next to the annotation counts.
