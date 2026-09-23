# fx-tests feedback

## Question: "does this failure follow the machine?" — the passing jobs of a config, and the worker of each job

- Commands: `fx-tests test <path> --task-ids --limit 0`, `fx-tests task <taskId> --profiles`.
- Expected: for a config, every job that ran the test (pass or fail), with its date, result and worker ID, so a per-worker
  pattern (every failing job fails both attempts, on a different machine each time) can be checked.
- Got: only failing task IDs; `fx-tests task` prints no worker ID, and states "No test-level failure" for passing jobs without saying
  whether the test ran in them.
- Workaround: Treeherder `api/project/<repo>/jobs/?job_type_name=...` for the job list, Taskcluster `task/<id>/status` for `workerId`,
  and a Python script over each job's `profile_resource-usage.json` to read this test's `Test` marker status. About 10 minutes and
  three ad-hoc scripts.
- What would have answered it: `fx-tests test <path> --config <c> --jobs` listing every job with result for this test and worker ID;
  or a worker column in `--task-ids`.

## Question: "which pass counts belong to which weekly job?"

- `fx-tests test <path> --config macosx1500 --history` gave e.g. 2026-09-14 `2 pass 4 fail`, which I could not map to jobs until I
  found (from resource-usage profiles) that the test runs in all four weekly jobs (`-1`/`-2` of media and media-spi), not in one
  chunk of each. Showing the job names behind a day's counts would have saved the detour.

## Failure-screenshot artifacts are not surfaced

- `fx-tests task <taskId> --profiles` lists profiles but not `public/test_info/mozilla-test-fail-screenshot_*.png`. For this test
  the screenshot was the decisive evidence (a macOS Screen Recording dialog). Listing it next to the profiles would make it found
  by default.
