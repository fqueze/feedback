## Which of the jobs starred on this bug actually show the bug's failure?

- Command: `fx-tests intermittent --bug 2071883 --limit 0`
- Expected: per occurrence (or grouped), whether the job's log carries the bug's summary message, so misstars stand out.
- Got: job name, task id, platform only. 17 of 247 stars were other failures (core22 esr apt errors, ftp.mozilla.org 503, mirror sync), only found by downloading all 24 non-majority logs and grepping them.
- Workaround: shell loop over `live_backing.log` URLs with grep.
- Could have shown: for build jobs with no TEST-UNEXPECTED line, the last `[taskcluster:error]`/`E:`/`Error` line of each log, or a count of occurrences whose log matches the bug summary's key phrase.

## Job name column truncated to the discriminator-less prefix

- Command: `fx-tests intermittent --bug 2071883 --limit 0` in a default-width terminal
- Expected: the job name column readable (all four names start with `snap-upstream-build-`).
- Got: `snap-upstre…` for every row; the part that differs (`arm64-local` / `amd64-esr` / …) was cut.
- Workaround: `COLUMNS=250`.

## `fx-tests task` on a build job leads with artifact expiry

- Command: `fx-tests task eyeafevkQEWjv5VguKni3g --profiles` (a same-day snap build task)
- Got: "has no profile_resource-usage.json … Taskcluster expires task artifacts after about a month, so this is permanent" before "A job that is not a test job never uploads one."
- Expected: for a task created today, lead with the not-a-test-job explanation (the task's name/kind is available from the queue).
