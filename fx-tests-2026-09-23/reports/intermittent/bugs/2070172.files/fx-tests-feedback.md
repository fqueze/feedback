# fx-tests feedback (2070172)

## Question: "has this non-test job (a toolchain task) been green since the fix landed?"

- Command: `fx-tests intermittent --bug 2070172 --since 30 --tree all --limit 0`
- Expected: some way to see the pass/fail history of the job named in the bug (`toolchain-linux64-custom-v8`, `toolchain-*-custom-car`), as `fx-tests test <path> --history` does for tests.
- Got: only the sheriff-annotated failures. Annotations stopping after 2026-09-12 cannot tell "fixed" apart from "starred elsewhere" or "stopped running". In fact the 2026-09-17 failures of the same jobs were starred on other bugs.
- Workaround: a Python script over `https://treeherder.mozilla.org/api/jobs/?job_type_name=<job>` to list results over time, plus the `bug-job-map` endpoint to see where each failure was starred. Note: that endpoint seems to ignore `repo=mozilla-central` and returns try jobs too, so revisions had to be checked against the logs.
- Could have shown: for a bug whose job names are not tests, a per-day pass/fail row for each named job (`--history` for jobs), and the bugs later failures of those jobs were starred on.
