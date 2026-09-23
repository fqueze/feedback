# fx-tests feedback

## A perftest-only test reads as "skipped everywhere", and its perftest failures are invisible

- Command: `fx-tests test toolkit/components/ml/tests/browser/browser_ml_engine_multi_perf.js` (also `--history`)
- Expected: the runs and failures of this test. It runs as a mochitest inside mozperftest jobs (`perftest-macosx-ml-multi-perf-{native,wasm}`), and bug 2069605 has 25 sheriff annotations for it.
- Got: `0 runs ... 5,509 skip` and `Verdict: skipped everywhere in this window`. Nothing says that perftest jobs are outside the data, so the verdict reads as "never runs".
- Workaround: `fx-tests intermittent --bug 2069605 --since 30 --tree all`, then the Treeherder jobs API by exact `job_type_name`, to get busted/success counts and when the job last ran.
- Could show: a note when the skip reason, or the manifest, says the test runs only as a perftest, saying that perftest runs are not covered.

## `fx-tests task` blames expiry for an artifact a perftest job never uploads

- Command: `fx-tests task ZQ9304poQTyCYojVbFIfjg --profiles`
- Expected: "this job uploads no profiles", since it is a perftest job. Its artifacts are only `perfherder-data-fetch-content.json` and the logs.
- Got: `has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent`. The task is two weeks old, so expiry is not the reason.
- Workaround: listing `/api/queue/v1/task/<id>/runs/0/artifacts` by hand.
