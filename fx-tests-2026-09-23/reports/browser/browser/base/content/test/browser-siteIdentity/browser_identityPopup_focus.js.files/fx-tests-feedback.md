## Question: which pushes did the failures happen on? (to find the culprit range of a step change)

- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_identityPopup_focus.js --task-ids --limit 0` (and `--json`)
- Expected: the revision (or push) of each failing task, or a per-push summary, since `--history` showed a 2-day burst that needs a culprit.
- Got: task IDs, job names and days only; no revision in the text or in the JSON `taskIds[]`.
- Workaround: a loop over 72 `queue/v1/task/<id>` calls to read `GECKO_HEAD_REV`, then Treeherder `push/?revision=` for each. It showed all 72 failing tasks on 5 autoland pushes in a 3-hour window, which pinned the culprit and the revert at once. A `revision` column (or a "failing pushes: first … last" line) would have answered this directly.

## `fx-tests task` prints an hg revision, and hg.mozilla.org answers 406 from this machine

- Command: `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/cf7bf384b191/<path>` (as the brief says)
- Expected: the file.
- Got: 302 to hg-edge.mozilla.org, then HTTP 406 with an empty body (also with a browser User-Agent; `json-pushes` too).
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<full hg rev>` gives the git hash, then `git show <git hash>:<path>` in the local checkout. Printing the git hash next to the hg one in `fx-tests task` would skip both hops.
