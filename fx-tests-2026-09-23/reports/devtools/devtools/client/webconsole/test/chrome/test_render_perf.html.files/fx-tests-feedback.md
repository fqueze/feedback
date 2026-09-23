## Question: which tasks on a try push failed this test, and with what message?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids`
- Expected: `devtools/client/webconsole/test/chrome/test_render_perf.html` listed as a perma-fail (it failed all 18 mochitest-chrome-1proc jobs, 6 configs x 3, each with `TEST-UNEXPECTED-TIMEOUT | ... | application timed out after 370.0 seconds with no output`).
- Got: the test is absent from every section; no mochitest-chrome-1proc job appears at all (only devtools-chrome jobs are listed). The header says 178 failed test job profiles were read.
- Then: `fx-tests try <rev> --all-jobs --test <path> --task-ids --profiles` shows 18/18 failed per config, but prints no task IDs or profile URLs despite `--task-ids --profiles`.
- Workaround: `treeherder-cli <rev> --filter mochitest-chrome-1proc --json` for Treeherder job IDs, then `curl https://treeherder.mozilla.org/api/project/try/jobs/<id>/` for each task_id. The `--test` output could have listed task IDs and failure messages per job.

## Question: is there any browser-side profile of a job whose browser was killed for an application timeout?

- Command: `fx-tests task NuPp0DM7TViKMjz1HydhPQ --profiles`
- Expected: the hang profile the harness captured (SIGUSR1/SIGUSR2, "Attempting to start the profiler to help with diagnosing the hang"), uploaded as `public/test_info/profile_0_2543.json`.
- Got: only the resource-usage profile, plus "No failing test named a per-test profile in this job."
- Workaround: spotted an `artifact profile_0_2543.json` marker in the resource-usage profile, then listed the task's artifacts with `curl .../runs/0/artifacts`. That profile was the most useful one for this failure: it showed the `unhandledrejection` and the idle main thread. `task --profiles` could list any `profile_*.json` artifact in `public/test_info/`.

