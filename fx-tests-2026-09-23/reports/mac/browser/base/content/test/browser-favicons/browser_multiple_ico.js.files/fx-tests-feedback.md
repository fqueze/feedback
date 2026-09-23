## Question: what is this test's history, when it was renamed inside the window?

- Command: `fx-tests test browser/base/content/test/browser-favicons/browser_multiple_ico.js --history`
- Expected: the 21-day history, or a note that the path only exists since 2026-09-18 and that `browser/base/content/test/favicons/browser_multiple_ico.js` has the earlier runs.
- Got: "Verdict: passing. 475 of 475 runs passed", with zero runs before 2026-09-18 and no hint why. The 89 timeouts were all under the old path.
- Workaround: `git log --follow` to find the old path (bug 2069131), then `fx-tests test <old path>`.
- What the output could show: "no runs before 2026-09-18: first seen that day" — or, better, fold in a same-named test under a sibling directory that stopped running the day this one started.

## Question: on which pushes, at what time, did this test fail — and where did it stop?

- Commands: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id>` per task for its revision, then `curl hg.mozilla.org/.../json-pushes?changeset=<rev>` per revision for the push id and date.
- Expected: the task list to carry repo, revision and push time, so the last failing push (the bound for "what stopped it") is one command.
- Got: task ids and job names only, grouped by day.
- Workaround: the loop above, one `fx-tests task` call per failure (~1 s each).
- What the output could show: `repo rev pushdate` next to each task id, and "last failure: autoland <rev> <date>" under `--history` when a test stops failing.
