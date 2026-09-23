## Question: are the failures after a fix on revisions that include the fix?

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_monitor_button.js --history`, then `--day 2026-09-18 --task-ids`
- Expected: some hint that the 5 failures dated 2026-09-18 ran on an old revision.
- Got: they are counted on the day the task ran. They were retriggers, created 2026-09-18, of mozilla-central b6333e929676, pushed 2026-09-10, from before the fix. `--history` makes them look like the failure came back 4 days after the fix. The 2 on 09-15 were the same kind of thing: 8c1187b2fe37, pushed 2026-09-14 21:43, without the fix.
- Workaround: `fx-tests task <id>` for the revision, then the Treeherder push API for its push date.
- Could show: the push date of the revision next to each task ID in `--task-ids`, or a flag on history rows whose failures ran on revisions pushed days earlier.

## `task --profiles` misses a per-test profile that was uploaded

- Command: `fx-tests task SDmTotRpRByWRNm_Kl-m_Q --profiles`
- Expected: a profile URL for browser_aiwindow_monitor_button.js. The job's resource-usage profile has `profile uploaded in profile_browser_aiwindow_monitor_button.js.json` for it at t=12m46s.
- Got: no profile line for that test. The profiles of the other failing test, browser_smartwindow_manage_tabs_tool.js, were listed.
- Workaround: read the resource-usage profile instead.
