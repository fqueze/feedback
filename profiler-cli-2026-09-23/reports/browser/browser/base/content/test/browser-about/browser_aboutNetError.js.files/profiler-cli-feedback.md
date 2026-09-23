# profiler-cli feedback — browser_aboutNetError.js

## `profile markers --search <host>` returns 538 KB because of one Preference Read payload

- Command: `profiler-cli profile markers --search 3des --session browser-browser_aboutNetError.js-1`
- Expected: the handful of network/page-load markers mentioning `3des.example.com`, one line each.
- Got: 538 KB of output. The mochitest PAC script (`network.proxy.autoconfig_url: data:text/plain,var knownOrigins = ...`) lists every test host, so each `Preference Read` marker for it matches and is printed with its full multi-KB value, unwrapped.
- Workaround: `--search '3des.example,-name:Preference Read'`.
- Suggestion: cut each list row's payload to the terminal width (as `thread markers --list` effectively does in practice), or summarise huge payload matches instead of printing them in full.

## Question the default output could not answer: which Glean event values differ between two page loads

- To compare the `securityUiTlserror.loadAbouttlserror` extras of the passing and failing error pages, the default `--search tlserror` list was enough (the extras are in the row). No script needed. Noting it as a case that worked well.
