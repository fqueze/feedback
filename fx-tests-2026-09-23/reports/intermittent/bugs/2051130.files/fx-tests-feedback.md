## Question: how often does a gtest fail, per config (failures over runs)?

- Command: `fx-tests test cubeb.loopback_duplex`
- Expected: per-config failure rate of the gtest, as for xpcshell/mochitest.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — gtest is not covered.
- `fx-tests intermittent --bug 2051130` gave the annotated jobs, but no denominator (runs per config).
- Workaround: Treeherder `api/failuresbybug/?bug=2051130&tree=all` for the failures and
  `api/project/autoland/jobs/?job_type_name=test-windows11-64-25h2-asan/opt-gtest-1proc&last_modified__gt=...`
  for the job counts, joined by task id in a script. What would have answered it: the gtest job
  counts per config next to the annotated counts, or gtest subtests in the `test` data.
