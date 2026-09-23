## `--bugs` prints nothing when no annotated bug exists, and misses a resolved bug naming the test

- Command: `COLUMNS=250 fx-tests test browser/components/places/tests/browser/browser_bookmark_add_tags.js --bugs`
- Expected: a "Bugs" section, "none" if empty, and ideally bugs whose summary names the test (bug 1775656 "Intermittent browser/components/places/tests/browser/browser_bookmark_add_tags.js | single tracking bug", RESOLVED INCOMPLETE on 2026-09-14, also cited in the manifest's `skip-if`).
- Got: the same output as without `--bugs`; no section at all. `--json` had `annotatedBugs: []`.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_bookmark_add_tags.js"`.

## Question: "which failing runs of this test logged error X before timing out" (splitting failure modes)

- Command: `fx-tests task <taskId> --messages` for each of the 27 failing tasks.
- Expected: the test's own error lines (here `Console message: [JavaScript Error: "Error: PageActions: No anchor node for bookmark" ...]`, which is what separates the two failure modes), or a way to filter failing runs by a substring of the test's log.
- Got: only `1x Test timed out`; every run looks the same.
- Workaround: downloaded each task's `public/logs/live_backing.log` and grepped the test's section (27 downloads). Could show: per failing run, the JavaScript errors / console errors logged during the test, or `fx-tests test <path> --task-ids --log-grep "<text>"`.
