# fx-tests feedback (browser_sync.js)

## Issues row shows a TEST-KNOWN-FAIL as the failure

- Command: `fx-tests test browser/base/content/test/sync/browser_sync.js`
- Expected: the Issues row ("first failure per run") names the run's first TEST-UNEXPECTED-FAIL.
- Got: `2408x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: appMenu-fxa-label2 ...`. In the per-test profile that message is a `TEST-KNOWN-FAIL` (non-a11y-checks job). The real failure, `TEST-UNEXPECTED-FAIL | sign-in promo is visible in the app menu when signed out`, comes 6 s later and appears nowhere in `fx-tests test`, `--json` `issues` or `messages`. `fx-tests task <id> --messages` has the same problem: it lists the known-fail a11y messages and pref-change messages next to the real one, all as if they were failures.
- Workaround: read the per-test profile's Test markers and look for TEST-UNEXPECTED-FAIL.
- Cost: I would have diagnosed the wrong message (the a11y-check one) without the profile.

## Question: which revisions did the failing tasks run on (to bracket the landing that stopped a failure)

- Command: `fx-tests test <path> --task-ids --limit 0 --day 2026-09-09 --json`, then a script fetching each task's `payload.env.GECKO_HEAD_REV` from the Taskcluster queue (233 requests), then lando hg2git for each revision.
- What the output could have shown: the revision (and push time) next to each task ID in `--task-ids`, or a "last failing revision / first passing revision after it" line when `--history` shows the failure stopping.
