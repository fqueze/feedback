# fx-tests feedback (browser_ext_request_permissions.js, 2026-09-22)

## Question: did any of this test's failures run on a revision containing the fix?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_request_permissions.js --task-ids --limit 0`
- Expected: next to each task, its repository, revision and push time, to compare with the
  fix's landing (autoland 2026-09-04 15:45 UTC).
- Got: task IDs, configs and failure dates only. The failure date is misleading here: three
  failures dated 2026-09-14 ran on revisions pushed 2026-08-15.
- Workaround: `fx-tests task <id>` per task for the revision, then the Treeherder push API for
  the push time (hg.mozilla.org answered 406 to `raw-file` and `json-rev`).

## Question: which run of the job is this profile from?

- Command: `fx-tests task b_7uPWjIQE-MqD-D_HpNRg --profiles --limit 0`
- Expected: with "2 failing executions of 2", either two profiles or a note that one is
  missing, and which run the listed one belongs to.
- Got: one profile, `profile_browser_ext_request_permissions.js.json`, without a `-2` suffix,
  so it reads as the first run's. It is the retry's: the first run's upload failed with
  `failed to upload profile: [Exception... "Out of Memory" ...]` (resource-usage profile,
  10m22s), so the retry got the unsuffixed name.
- Workaround: loading the profile and seeing `-2` uploads of the tests before it, then the
  resource-usage profile. Labelling each profile with its run, and reporting a failed upload,
  would answer it.

## Question: what is this one test's profile in this job?

- Command: `fx-tests task QpSrFmkpR1Gt5mBA-g7xLw --profiles`
- Expected: a way to ask for one test's row.
- Got: the first 20 failing tests (this one was the 21st), then `… 24 more`.
- Workaround: `--limit 0` and grep. A `--test <path>` filter would answer it directly.
