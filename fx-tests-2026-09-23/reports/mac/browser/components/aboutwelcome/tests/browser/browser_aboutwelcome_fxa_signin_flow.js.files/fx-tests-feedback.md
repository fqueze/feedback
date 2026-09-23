# fx-tests feedback (browser_aboutwelcome_fxa_signin_flow.js)

## `--bugs` prints nothing when it finds no bug

- Command: `fx-tests test browser/components/aboutwelcome/tests/browser/browser_aboutwelcome_fxa_signin_flow.js --bugs`
- Expected: a line saying no sheriff-annotated bug names this test (and ideally that bug 1974292, its RESOLVED single tracking bug, exists).
- Got: the same output as without `--bugs`, no line at all; only `--json` showed `"annotatedBugs": []`.
- Workaround: `--json`, then a Bugzilla REST summary search to find the resolved tracking bug.

## Question: "does test B fail in every job where test A fails?"

- The victim/leaker check needed the failing task IDs of two tests in the same manifest compared as sets.
- Command: `fx-tests test <A> --task-ids --limit 0` and `fx-tests test <B> --task-ids --limit 0`, then `comm` over the extracted IDs (a script).
- Answer: all 27 failing jobs of `browser_aboutwelcome_fxa_signin_flow.js` also had `browser_aboutwelcome_campaign_actions.js` failing; the 4 campaign-only jobs were all `-standalone`.
- What could have shown it: `fx-tests task --profiles` already lists what else failed per job; a `fx-tests test <path> --co-failures` (tests that failed in the same jobs, with counts, same manifest first) would answer it directly.
