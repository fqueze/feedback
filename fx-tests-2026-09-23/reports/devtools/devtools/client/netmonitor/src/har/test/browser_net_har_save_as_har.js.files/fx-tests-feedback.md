## Question: "which bugs name this test?" — `--bugs` prints nothing

- Command: `fx-tests test devtools/client/netmonitor/src/har/test/browser_net_har_save_as_har.js --bugs`
- Expected: a Bugs section, even if empty ("no open bug names this test"), ideally listing closed tracking bugs too.
- Got: the same output as without `--bugs`, with no Bugs section and no "none" line. So I could not tell "no bug" from "flag ignored".
- Workaround: searched Bugzilla by summary by hand. It turned up bug 1948160 ("Intermittent ...browser_net_har_save_as_har.js | single tracking bug", RESOLVED INCOMPLETE) and bug 1862292 (same for the sibling browser_net_har_post_data.js, RESOLVED INCOMPLETE on 2026-08-31).
