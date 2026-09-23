## Question: which pushes first and last failed, to find the landing behind a step change

- Command: `fx-tests test browser/base/content/test/siteIdentity/browser_mixedcontent_securityflags.js --task-ids --limit 0` (and `--json`)
- Expected: for a step change (`--history` showed 146 failures on 2026-09-10/11 only), the revision / push id / push time of each failing task, or directly the first and last failing push.
- Got: task ids grouped by day only; the JSON `taskIds[]` has no revision or time either.
- Workaround: a script fetching each of the 73 task definitions from the Taskcluster queue (`payload.env.GECKO_HEAD_REV`), then Treeherder `push/?revision=` for push ids and times, then the Treeherder push list around them to spot the landing and its revert. The output could have shown, per failing task, the repo+revision, or a "Failing pushes: first <rev> (<time>), last <rev> (<time>)" line when the failures cluster.
