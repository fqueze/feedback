## `--bugs` prints nothing when no open bug names the test

- Command: `fx-tests test devtools/client/netmonitor/test/browser_net_curl-utils.js --bugs`
- Expected: a Bugs section, even if only "no open bug names this test", ideally also listing closed ones such as bug 1767638 ("Intermittent devtools/client/netmonitor/test/browser_net_curl-utils.js | single tracking bug", RESOLVED INCOMPLETE on 2026-06-08).
- Got: the exact same output as without `--bugs`, with no Bugs section at all, so I could not tell "searched and found none" from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=curl-utils`.
