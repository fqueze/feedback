## Fallout counted as separate failure modes, and `--issue` hides the jobs

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_ctrlTab.js` then `--task-ids --issue 1` / `--issue 2` / `--issue 3`.
- Expected: the "Issues (first failure per run)" list to tell me whether issues 2 and 3 (`leaked 1 window(s) until shutdown [... dummy_page.html]`, `leaked 1 docShell(s)`) are separate failure modes or fallout of issue 1 (`Every preview should have a thumbnail`).
- Got: 696 / 133 / 18 as if they were distinct modes, and the job sets are disjoint (117 issue-2 jobs, none in the issue-1 list). Spot-checking five issue-2/3 jobs with `fx-tests task <id> --messages` showed every one also has `Every preview should have a thumbnail` and `Test timed out`: in debug builds the shutdown leak sorts first, so "first failure" picks it and the job drops out of `--issue 1`.
- Workaround: diff the `--issue N` task lists and read `fx-tests task --messages` for a sample.
- Question the default output could have answered: "which failure modes co-occur in the same job?" A per-issue "also in this job: issue 1 (n of m)" line, or ordering by the first test-level failure before shutdown leaks, would have answered it.
