# fx-tests feedback (browser_ext_management.js)

## `test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_management.js --bugs`
- Expected: a "Bugs" block, saying "none" when Treeherder/Bugzilla return nothing.
- Got: the ordinary `test` output with no mention of bugs at all, so I could not tell "no bug"
  from "the lookup silently failed". `--json` had `"annotatedBugs": []`.
- Workaround: read `annotatedBugs` from `--json`.

## Question: "was this task's revision before or after fix X landed?"

- Command: `fx-tests task <taskId>` prints `autoland a78ff5819100` and a Treeherder URL with
  the 40-hex hg hash.
- The question: whether the 2026-09-14 failures ran on revisions that contain a fix landed
  2026-09-04. They did not: they were jobs on 2026-08-15 pushes. Nothing in the output says the
  push is three weeks older than the run date that `test --task-ids` files them under.
- `curl https://hg.mozilla.org/integration/autoland/raw-file/<rev>/...` answered HTTP 406 from
  hg-edge for every request after the first, so reading the test at the failing revision the
  way the brief says was not possible.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<hg hash>` for the git hash, then
  `git merge-base --is-ancestor <fix> <git hash>` and `git show <git hash>:<path>`.
- What would have answered it: print the push date and the git hash next to the hg revision in
  `task`, and flag in `test --task-ids` a task whose push is much older than its run date.
