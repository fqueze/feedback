## `thread network` hides why a failed request failed

- Command: `profiler-cli thread network --sort start --limit 0 --search "fallback.org" --session test_trr_https_fallback.js-1`
- Expected: the failed request's `requestStatus` (here `NS_ERROR_NET_PARTIAL_TRANSFER`, on macOS `SSL_ERROR_RX_RECORD_TOO_LONG`) next to the `???` status, since that is the one thing a failing test needs from the network view.
- Got: `???  cache=Unresolved  duration=43.077ms` and the phases; the error only appears in `marker info m-537`. Also the summary says "0 failed" while the profile holds failed requests.
- Workaround: `marker info` on each `???` row.
