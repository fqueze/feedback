## Which job each occurrence of a bug is (bug 1795826)
- Command: `fx-tests intermittent --bug 1795826`
- Expected: the Occurrences table to tell which job and platform each task ID is.
- Got: `job name` and `platform` columns cut to `browsertime…` / `macosx1470-…`, the discriminator here since the bug covers 8 different jobs; the full `testSuite` was only in `--json` `occurrenceRows`.
- Workaround: `--json` and a script.

## Pass/fail history of a non-test-file job (raptor/browsertime)
- Question: "how often does `test-macosx1470-64-shippable/opt-browsertime-benchmark-safari-motionmark-1-3` fail, and which task IDs are its passing runs?"
- Command tried: none fits; `fx-tests test` takes a test path, and raptor jobs have none.
- Workaround: Treeherder `api/project/mozilla-central/jobs/?job_type_name=...`.
- What could answer it: a `fx-tests job <job name> --history --task-ids`.
