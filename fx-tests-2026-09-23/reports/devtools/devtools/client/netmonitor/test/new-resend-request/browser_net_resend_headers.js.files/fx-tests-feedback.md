## `--bugs` prints nothing when the only bug naming the test is resolved

- Command: `fx-tests test devtools/client/netmonitor/test/new-resend-request/browser_net_resend_headers.js --bugs`
- Expected: a Bugs section listing bug 1767651 ("Intermittent devtools/client/netmonitor/test/browser_net_resend_headers.js | single tracking bug", RESOLVED INCOMPLETE), or at least an explicit "no open bug names this test".
- Got: the normal `test` output with no Bugs section at all, on stdout or stderr. Silence reads the same as "flag ignored".
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_net_resend_headers'`. Note the bug names the test's old path (without `new-resend-request/`), which may be why it was not matched.
