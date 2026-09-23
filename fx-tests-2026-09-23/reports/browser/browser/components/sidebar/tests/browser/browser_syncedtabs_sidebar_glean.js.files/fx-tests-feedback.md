## Question: which git revision did this job run, so I can read the test as it ran?
- Command: `fx-tests task NWcmPl12Tzu3lOzc_B7h7g --profiles`
- Expected: a revision I can read files at.
- Got: `autoland bfbfaae693f3` (short hg hash). `curl https://hg.mozilla.org/integration/autoland/raw-file/bfbfaae693f3/...` now returns HTTP 406 (with or without a User-Agent), and `https://lando.moz.tools/api/hg2git/firefox/<hash>` 404s on a short hash, so the full hash has to be scraped from the Treeherder URL line.
- Workaround: take the 40-char hash from `revision=` in the Treeherder URL, map it with lando hg2git, then `git show <git>:<path>`.
- What the output could show: the full hg hash and the git hash next to it.

## Question: did the failures after a fix landed run on revisions that contain the fix?
- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_syncedtabs_sidebar_glean.js --history`
- Expected: a day with failures to mean the test failed on code current that day.
- Got: 14 failures on 2026-09-14 that look like a recurrence, but every one ran on an autoland revision committed 2026-08-14/15 (retriggers or backfills of old pushes), two weeks before the fix. Same for the 09-02 failures (revisions from 08-27 and 09-01). Telling them apart took a loop of `fx-tests task <id>` (26 calls) + lando hg2git + `git merge-base --is-ancestor`.
- What the output could show: in `--history` or `--task-ids`, the push date of each failing job's revision (or flag jobs whose revision is much older than the run date).

## Question: the test's source at this job's revision (reviewer, browser-review-browser_syncedtabs_sidebar_glean.js)
- Command: `fx-tests task RuvuBfS-S8y2NgosF8eKWw` prints `autoland b680520ae7e7`; then `curl -sL https://hg.mozilla.org/integration/autoland/raw-file/b680520ae7e7/<path>` (as subagent-brief.md says).
- Expected: the file.
- Got: HTTP 406 and an empty body, with the short hash and with the full one from the Treeherder URL (the same call worked for `bfbfaae693f3`, a 09-01 revision).
- Workaround: `https://lando.moz.tools/api/hg2git/firefox/<full hg hash>`, then `git show <git hash>:<path>` in the local checkout.
- What the output could show: the git hash next to the hg one, so `git show` works directly.
