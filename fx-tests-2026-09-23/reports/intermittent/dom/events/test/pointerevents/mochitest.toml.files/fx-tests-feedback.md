## A manifest-scoped leakcheck failure cannot be looked up

- Question: "in which jobs, annotated or not, does `leakcheck large nsGlobalWindowInner | dom/events/test/pointerevents/mochitest.toml` happen, and does it follow every timeout of `test_pointerrawupdate_event_count_touch.html`?"
- Command: `fx-tests test dom/events/test/pointerevents/mochitest.toml`
- Expected: the leakcheck failures scoped to that manifest, with per-config counts and task IDs, like a test's failures.
- Got: `No test path in the xpcshell and mochitest 21-day data contains "dom/events/test/pointerevents/mochitest.toml"`. `fx-tests task <id>` on a leaking job lists only the test failure, not the leak (the job's `mochitest-plain_errorsummary.log` has no leak line either, and the group status is `OK`).
- Workaround: `fx-tests intermittent --bug 2034904` for the annotated jobs, then `fx-tests test <the hanging test> --task-ids --limit 0` and grepping 53 `live_backing.log`s for `leakcheck large`. All 53 had it.
- What would have answered it: leakcheck failures recorded per job with their scope, so `fx-tests test <manifest>` or `fx-tests task <id>` could list them next to the test failures of the same browser session.
