# fx-tests feedback (browser_breachAlertLinkTelemetry.js)

## Question: "on which days did each failure mode happen?" (does one mode carry the step change?)

- Command: `fx-tests test browser/components/aboutlogins/tests/browser/browser_breachAlertLinkTelemetry.js --history`
- Expected: the per-day history split by Issue row (or an `--issue <n>` filter that applies to `--history`), since the test has two main failure modes and only one of them stepped up.
- Got: per-day pass/fail totals only; `--issue` only applies to `--task-ids`.
- Workaround: 21 separate `fx-tests test <path> --day 2026-09-DD` runs, grepping the Issues block of each. 42 invocations in total to also get the run counts.
- What could have shown it: an Issues x day table under `--history`, or `--history --issue <n>`.

## Question: "at which git commit do I read the source of this job?" (review, browser-review-browser_breachAlertLinkTelemetry.js)

- Command: `fx-tests task G78TaceeR_uqtkoFkap_vg --profiles`
- Expected: the job's revision in a form I can read the source at, i.e. its git hash as well as the hg one.
- Got: `autoland 93cd60ebd99d` only. `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/93cd60ebd99d/<path>` now answers HTTP 406 through hg-edge and writes an empty file. The report's author left empty `head.js` and `net-error-card.mjs` here from the same failure, and fell back to origin/main.
- Workaround: `curl -s https://lando.moz.tools/api/hg2git/firefox/<full hg hash>` gives the git hash (1226b3963f72), then `git show <hash>:<path>` in the local checkout.
- What could have shown it: the git hash next to the hg one in `fx-tests task` output.
