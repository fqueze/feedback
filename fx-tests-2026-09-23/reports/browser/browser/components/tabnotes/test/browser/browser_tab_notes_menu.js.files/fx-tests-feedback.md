## Question: which subtest (add_task) timed out, per failing job

- Command: `fx-tests test browser/components/tabnotes/test/browser/browser_tab_notes_menu.js --task-ids --limit 0 --issue 1 --full-messages`, then `fx-tests task <id> --json`.
- Expected: the 29 timeouts split by the subtest named in the log line (`TEST-FAIL | ... | test_openTabNotePanelFromContextMenu - Test timed out` vs `test_ineligibleTabsDisableMenus - Test timed out`), since that is what separates the failure modes here (21 Linux in one subtest, 8 macOS in another, two unrelated causes).
- Got: every timeout is the single issue `TIMEOUT Test exceeded time limit`, and `task --json` carries only `messages: ["Test timed out"]` — no subtest name.
- Workaround: downloaded all 33 `live_backing.log` files and grepped `TEST-(UNEXPECTED-)?(FAIL|TIMEOUT).*<test>`. Could have shown: the subtest name as part of the message (or an issue key), so `--issue` separates the modes.
