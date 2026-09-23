## `fx-tests test <path> --bugs` is silent when no bug names the test

- Command: `fx-tests test devtools/client/netmonitor/test/new-resend-request/browser_net_new_request_panel_persisted_content.js --bugs`
- Expected: a line such as "Bugs: none name this test".
- Got: the same output as without `--bugs`, with no bug section at all, so "no bug" cannot be told apart from "the flag did nothing".
- Workaround: took the silence to mean no bug.
