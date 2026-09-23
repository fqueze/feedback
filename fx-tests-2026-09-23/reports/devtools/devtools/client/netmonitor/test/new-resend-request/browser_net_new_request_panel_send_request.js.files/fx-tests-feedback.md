## `fx-tests test <path> --bugs` prints no bug section

- Command: `fx-tests test devtools/client/netmonitor/test/new-resend-request/browser_net_new_request_panel_send_request.js --bugs`
- Expected: bug 1767648 ("Intermittent .../browser_net_new_request_panel_send_request.js | single tracking bug", RESOLVED INCOMPLETE by BugBot on 2026-08-10) and dup bug 1761372, or an explicit "no bug found".
- Got: the same output as without `--bugs`; no Bugs section at all, so "no bug" and "flag ignored" look identical.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=<test file name>'`.
