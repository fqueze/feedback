## Question: "how did my test fail in this job, and does it have a per-test profile?"
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles` (a mass-failure job with 488 failing tests).
- Expected: a way to restrict the job view to one test (e.g. `--test <path>`), or the named test surfaced first.
- Got: the first 20 failing tests alphabetically, `… 468 more`; had to rerun with `--limit 0 --full-messages` and grep, six times (once per job).
- Workaround: `--limit 0` into a file, then `rg <test name>`.
