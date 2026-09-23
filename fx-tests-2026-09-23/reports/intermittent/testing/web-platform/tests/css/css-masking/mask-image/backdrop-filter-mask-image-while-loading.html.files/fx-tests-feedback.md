## Question: how often does this WPT reftest fail on each config, and did it pass in the other jobs?

- Command: `fx-tests test testing/web-platform/tests/css/css-masking/mask-image/backdrop-filter-mask-image-while-loading.html` (also `--history`)
- Expected: per-config pass/fail rates and a per-day history, as for mochitest/xpcshell.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". There is no WPT coverage, although `fx-tests intermittent --bug` and `fx-tests task <id>` both handled WPT jobs fine.
- Workaround (cost: about 15 tool calls and several minutes): list the tsan reftest jobs per chunk from Treeherder's `/api/project/<repo>/jobs/?job_type_name=...`, keep the jobs whose `wpt_errorsummary.log` first line lists the test's group, then read the test's status from each `wptreport.json`. `/api/jobs/?repo=` silently ignores `repo`, and a push's jobs list truncates at 2000 unless you paginate, and both of these cost retries.
- What would have answered it: `fx-tests test` over WPT (the wptreport.json files have every result), plus a way to show two tests' outcomes side by side per job (`--with <other test>`), since this failure is 100% correlated with an earlier test in the same group.
