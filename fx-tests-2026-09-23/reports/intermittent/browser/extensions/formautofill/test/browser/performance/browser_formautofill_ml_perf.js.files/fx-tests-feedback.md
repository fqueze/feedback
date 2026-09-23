## Perftest-only mochitests look "skipped everywhere, no runs at all"

- Command: `fx-tests test browser/extensions/formautofill/test/browser/performance/browser_formautofill_ml_perf.js` (and `--history`)
- Expected: the runs of this test, which runs only under mozperftest (`perftest.toml`, `perftest-*-formautofill-ml-perf-*` jobs) and perma-failed there on 2026-09-15 (bug 2072109, 106 annotations).
- Got: `0 runs ... 1,915 skip`, `Verdict: skipped everywhere in this window`, and the skip reason "Disabled because this is only run as a performance test". This reads as "never runs" and hides a perma-failure.
- Question it could not answer: "Does this test fail in its perftest jobs, and has it passed since the fix?" A verdict line saying "runs only as a perftest; perftest jobs are not in this data" would have saved a detour.
- Workaround: `treeherder-cli --repo mozilla-central --similar-history <jobId from fx-tests intermittent --json occurrenceRows> --json`, plus a script to print dates.

## `fx-tests task` on a perftest task

- Command: `fx-tests task LhTxvl_qQHOhCZ7C9Lnb7A --profiles`
- Got: "has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent". The task is 7 days old, and its other artifacts expire in 2027. Perftest tasks never upload that profile, so the expiry explanation is wrong here.
- Expected: "this is a perftest job, which uploads no profiles", or at least no claim that the artifact expired.

## `fx-tests intermittent --bug` without `--tree all --since`

- Command: `fx-tests intermittent --bug 2072109`
- Got: "no sheriff annotations ... on trunk between 2026-09-16 and 2026-09-22". All 106 annotations are on mozilla-central on 2026-09-15, one day before the default window. The hint to widen was there and it worked. Mentioning the date of the bug's last annotation would have answered the question directly.
