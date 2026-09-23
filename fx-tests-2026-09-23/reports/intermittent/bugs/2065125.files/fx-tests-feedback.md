## Question: failure rate and history of a talos job (not a mochitest/xpcshell test)

- Command: `fx-tests test tp5n`
- Expected: per-config pass/fail counts and `--history` for the talos-xperf tp5n job, which is what bug 2065125 is about.
- Got: `No test path in the xpcshell and mochitest 21-day data contains "tp5n"`. `fx-tests task <talos task> --profiles` said "This profile records no tests at all". `fx-tests intermittent --bug 2065125` gave only the sheriff-starred jobs, with no denominator.
- Workaround: scripted the Treeherder `jobs/?job_type_name=...` API for the two job names on autoland and mozilla-central, plus `jobs/<id>/text_log_errors/` to split this failure from unrelated ones (a certifi/tooltool burst on the same jobs). That took about 10 tool calls.
- What would have answered it: an `fx-tests job <job name>` (or `test` accepting a job name for harnesses without per-test data). It would give runs, failures by message, and per-day history.

## Question: on which workers, regions and images did the failures run, compared with the passes?

- Command: `fx-tests intermittent --bug 2065125 --limit 0` (and `fx-tests task <id>`)
- Expected: something that shows the failures cluster on one kind of machine.
- Got: task ids with tree/platform/job only. The decisive signal was the Azure region: 99/410 in westus/westus2/westus3, 0/700 elsewhere. The second signal was the worker image version (sbom) that started the failures. Neither shows up anywhere.
- Workaround: the Taskcluster `task/<id>/status` API per run for `workerGroup` (1464 calls), plus downloading 590 full logs to read the worker-type settings header (`sbom`, `instance-type`).
- What would have answered it: a `workerGroup` column (and the image/sbom from the log header) in `task` and `intermittent --bug` output. Better still, a pass/fail split by workerGroup and image for a job.
