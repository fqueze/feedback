## Question: on which jobs/configs did the passing runs of a newly-failing test run, on a try push?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs`
- Expected: for the NEW INTERMITTENTS row `browser_net_new_request_panel_context_menu.js  2/4`, a line naming the jobs of the 2 passing runs (the skip-if removed was a11y-checks only, so I needed to know whether any other a11y-checks job ran it and passed).
- Got: the row says `2/4` and the detail block names only the failing job (`test-linux2404-64/opt-mochitest-devtools-chrome-a11y-checks-4`). Even `--json` only gives counts (`totalRuns: 4, totalJobs: 3, outcomes.passed: 2`) with no job names for the passes.
- Workaround: none; inferred from `failedTwice: 1, passed: 2, totalJobs: 3` that the passes were in 2 other jobs, and assumed from the manifest that they were non-a11y-checks configs.
- What would have answered it: a "passed on: <job names>" line under the row (at least with `--all-jobs`, which already read those jobs' profiles).

Correction to the entry above: the 2 passes were in the other two `a11y-checks-4` jobs of the push (EZMJjdHHSt2eWvMDY4LxzA, UbNR4DXPQf-WZQpDmMhzwA), on the same config as the failure. I only found that by grepping the `--json` for every task named `a11y-checks-4` and running `fx-tests task` on each. Without that, the default output reads as "fails every a11y-checks run" — the opposite of the truth (1 of 3 jobs). The row lists the config under `jobNames` with no count of how many jobs of that config ran it, so a 1-of-3 config looks like a 1-of-1 perma-fail.
