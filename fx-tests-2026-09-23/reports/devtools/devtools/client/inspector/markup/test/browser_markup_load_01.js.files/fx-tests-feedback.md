## `fx-tests task --messages` drops messages silently, and ranks the first failure last

- Command: `fx-tests task bCWxwzOCQrixDHeX-SDAjg --messages`
- Expected: every failure message for `browser_markup_load_01.js` (help says "cap 20"), or a "+N more" line; ideally the chronologically first failure first.
- Got: 5 messages (unexpected popup, 4 leak lines), no "+N more" line. The `--json` `allMessages` has 7: the actual first failure, `Uncaught exception in test - TypeError: ... this.targetFront is null`, and the `bootstrap` rejection were omitted. The headline message chosen was `leaked 1 window(s) until shutdown`, which is fallout.
- Workaround: `--json` and read `allMessages`. Cost: I almost reported debug builds as a separate failure mode (leaks without the TypeError).

## Question: "the task IDs and profile URLs behind one test's failures on a try push"

- Command: `fx-tests try <rev> --test <path> --all-jobs --task-ids --profiles`
- Expected: per-config table plus the task IDs and profile URLs for that test.
- Got: only the per-config table; `--task-ids`/`--profiles` ignored with `--test`. Without `--test`, `--limit 0 --task-ids` still cuts each row's task list to 5 ("… 33 more tasks") and profiles to 5 ("… 14 more profiles"), and there is no flag to lift that.
- Workaround: `fx-tests try <rev> --limit 0 --task-ids --profiles --json`, then a script over `permaFails[].taskIds`/`profiles` filtered by path.

## Question: "where was this uncaught exception thrown" (review-browser_markup_load_01.js)

- Command: `fx-tests task GiNx5-72Rpi5Oo6PEjXgUw --json`, reading `failures[].messages` and `allMessages`
- Expected: the JS stack the harness logs right after `Uncaught exception in test - TypeError: ...`.
- Got: the message line only. The raw `live_backing.log` has the stack on the following INFO lines (`getNodeActorFromContentDomReference@.../fronts/inspector.js:271:9`, `inspectNode@.../devtools.js:902:38`, ...). The profile's TEST-UNEXPECTED-FAIL marker stack is the harness's (`addResult`/`handleTask`), not the exception's, so neither tool answered it.
- Workaround: `curl` the task's `public/logs/live_backing.log` and `rg -A 12` the message. Attaching the following stack lines to the message would have answered it.
