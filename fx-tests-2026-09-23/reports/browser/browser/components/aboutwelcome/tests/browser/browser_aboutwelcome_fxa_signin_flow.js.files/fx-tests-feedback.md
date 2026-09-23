## `--bugs` prints nothing when no open bug names the test

- Command: `fx-tests test browser/components/aboutwelcome/tests/browser/browser_aboutwelcome_fxa_signin_flow.js --harness mochitest --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test" line; ideally also closed bugs (bug 1974292, the test's own "single tracking bug", closed WONTFIX 2025-09-08, and bug 1843061 for its old path).
- Got: the same output as without `--bugs`, no Bugs section at all; `--json` has `annotatedBugs: []` and no bug field. Cannot tell "no bug" from "flag ignored".
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_aboutwelcome_fxa_signin_flow"`.

## Question: which other tests failed in the same jobs as this one, across all its failing jobs?

- Needed to see that a neighbour (`browser_aboutwelcome_campaign_actions.js`) failed first in every one of the 27 failing jobs.
- Command: a shell loop of `fx-tests task <id>` over the 27 task IDs from `fx-tests test <path> --task-ids --limit 0`, grepping each output (slow: one profile fetch per job).
- What could show it: `fx-tests test <path> --co-failures` — per other test, in how many of this test's failing jobs it also failed, and whether it ran before this one.

## Question: in which of test A's failing jobs did test B not fail (and vice versa)?

- Needed to check the leaker/victim correspondence: every non-standalone campaign_actions failure is followed by an fxa timeout; the 4 extra campaign_actions failures are all `-standalone` jobs.
- Command: `comm` over the sorted `--task-ids --limit 0` outputs of both tests.
- What could show it: the co-failure view above, with the jobs where only one of the two failed.
