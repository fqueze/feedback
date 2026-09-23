## Question: what was the underlying error in each annotated job of a generic perftest signature

- Bug 2056657 is a generic symptom (`MetricsMissingResultsError: Could not find any results to process.`): the real error is the script traceback printed just before it, and it differs by job.
- `fx-tests intermittent --bug 2056657 --tree all --since 60` lists 772 occurrences, but "Failure messages, per annotated job" shows only 8 unrelated lines (lambdatest, mochitest). In `--json`, every perftest `occurrenceRows[].lines` is `[]`, so the tool has no failure lines for perftest jobs at all.
- What I needed: for each annotated job, its error lines (Treeherder's parsed failure lines, or the last exception in the log), grouped. Here that would have shown `__main__.InvalidLastFrame` in 633 of them, `AndroidStartUpMatchingError` in 80, and so on.
- Workaround: downloaded all 772 `live_backing.log` (about 100 MB) and classified them with a script (`classify.py` here). About 5 minutes, but 100 MB of downloads.

## Question: which of a perftest job's artifacts can stand in for a profile

- `fx-tests task T38-0NRFS0-NXRvtBLZPQQ --profiles` (a perftest job from 2026-09-10, 12 days old) exits 4 with "has no profile_resource-usage.json ... Taskcluster expires task artifacts after about a month, so this is permanent". The artifact was never uploaded, because perftest jobs do not upload one. Blaming expiry is misleading when the task is 12 days old.
- Expected: something like "this job type does not upload profiles", plus the task's artifact list. Here that list includes `public/build/<test>.tgz`, which holds the per-iteration screenshots and videos, and those were the evidence.
- Workaround: listed the artifacts with `curl .../runs/0/artifacts`.

## Question: is the bug still failing, or did only its message change

- `fx-tests intermittent --bug 2056657` shows no annotations after 2026-09-08. Nothing in the output hints that the same jobs are now starred on other bugs (2070794, 2071660, 2070405). That happened because a harness change on 2026-09-08 (bug 2059094) changed the failure line.
- What would have shown it: for the bug's job names, the bugs their failures were starred on after the last occurrence. Workaround: Treeherder `/api/project/<repo>/jobs/?job_type_name=...` plus `/bug-job-map/?job_id=...` for every failed job (`th_rates.py` here, plus an inline script).
