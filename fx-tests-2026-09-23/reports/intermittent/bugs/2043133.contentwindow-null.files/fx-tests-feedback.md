## `fx-tests task` without the run suffix, on a task whose failing run is not 0

- Command: `fx-tests task ZQZwM6_pSc6kCR3SqiDbJA --profiles` (the `--task-ids` list printed `ZQZwM6_pSc6kCR3SqiDbJA.2`; I dropped the suffix).
- Expected: the latest run, or "run 0 has no artifact; runs 1 and 2 exist".
- Got: exit 4, "has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent". The hint about `.0` being assumed is there, but the "expired, permanent" wording comes first and points the wrong way.
- Workaround: pass `.2`.

## Android: "No failing test named a per-test profile in this job"

- Command: `fx-tests task ZQZwM6_pSc6kCR3SqiDbJA.2 --profiles` (test-android-em-14-x86_64-lite/opt-geckoview-xpcshell-nofis-2).
- Question: where is the runtime evidence for an Android xpcshell failure?
- Got: the sentence above, which reads as if this job just happened not to upload one. Android xpcshell jobs never do (the device records one under `MOZ_PROFILER_STARTUP=1`, but the harness does not pull it). The job does upload `public/test_info/logcat-emulator-5554.log`, where every test log line carries the parent pid and a timestamp, next to the content processes' output.
- Could show: "Android jobs upload no per-test profiles; see the logcat artifact <url>".
