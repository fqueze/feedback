## `--bugs` prints nothing at all when it finds no bug

- Command: `COLUMNS=200 fx-tests test browser/base/content/test/browser-popupNotifications/browser_popupNotification_accesskey.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs`; no line about bugs. Bugzilla does have bugs naming the test under its previous path (`browser/base/content/test/popupNotifications/...`): bug 2001793 (Perma, RESOLVED DUPLICATE of 2001881) and bug 1913659 (RESOLVED INCOMPLETE). Silence reads like "no bug", and it cannot be told from "the lookup did not run", nor from "only open bugs under the current path were searched".
- Workaround: `curl -s "https://bugzilla.mozilla.org/rest/bug?summary=browser_popupNotification_accesskey&include_fields=id,summary,status,resolution"`.

## `try --profiles` lists only the first-run profile per task, not the retry's `-2`

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- Expected: both per-test profiles for a task whose first run and retry both failed (the summary does say `failedTwice: 6`).
- Got: one `test profile` URL per task (`profile_browser_popupNotification_accesskey.js.json`); the `-2.js.json` URL only shows up in `fx-tests task <taskId> --profiles`.
- Workaround: run `fx-tests task <taskId> --profiles` per task.
