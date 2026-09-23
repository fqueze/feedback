## `fx-tests test` "Issues (first failure per run)" takes a TEST-KNOWN-FAIL message as the failure

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_groups_tabContextMenu.js`
- Expected: the issues to be the first *unexpected* failure of each run.
- Got: issue 3, `50x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: context_moveTabToNewGroup ...`, listed as a failure mode of its own, on macOS only. In the per-test profile of X6_aYtyfTSKfntJG4b7hPg that message is `TEST-KNOWN-FAIL` (a todo: macOS native context menus have no frames), and the job's only `TEST-UNEXPECTED-FAIL` is `group2 menu item has correct label - null == "Unnamed group"` — issue 2. `fx-tests task --messages` on all 41 macOS tasks listed under `--issue 3` shows the group2 message in every one. So one failure is split into two issues of 55 and 50, by platform, and it reads as two different bugs.
- Workaround: `fx-tests task <id> --messages` per task, then the per-test profile's `TEST-UNEXPECTED-FAIL` marker.

## `fx-tests task --messages` mixes todo messages with failures

- Command: `fx-tests task TsWjpJHbT3SZ0a6kOcE6pA --messages`
- Got: 8 messages under `FAIL — 2 failing executions of 2`, sorted alphabetically, with no status: 7 of them (`Clicked menuitem ... which was not open`, `handleEvent() was unable to perform a11y checks ...`, `changed preference: browser.tabs.splitview.hasUsed`) are `TEST-KNOWN-FAIL` in the profile, and the one unexpected failure is fifth in the list.
- Expected: the status of each message, or the unexpected ones first.
