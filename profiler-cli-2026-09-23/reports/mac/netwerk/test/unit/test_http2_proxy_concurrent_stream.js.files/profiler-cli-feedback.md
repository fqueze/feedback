## `thread network` reports "0 failed" for requests that ended in NS_ERROR_*

- Command: `profiler-cli thread network --session <s> --sort start --limit 0` on the per-test profile of task Aa0diT8ESIG012fL88fnzw (test_http2_proxy_concurrent_stream.js).
- Question: which requests failed, and with which error.
- Expected: the 20 requests that ended with `requestStatus: NS_ERROR_NET_RESET` / `NS_ERROR_NET_PARTIAL_TRANSFER` / `NS_ERROR_PROXY_CONNECTION_REFUSED` counted as failed in the summary, and the `requestStatus` shown on each row.
- Got: `171 completed requests ... 0 failed`; the failed rows show only `???  cache=Unresolved  duration=...`, no status.
- Workaround: `profiler-cli marker info m-768 m-769 ...` on each `???` row to read `requestStatus`.

## Test log lists every assertion twice

- Command: `profiler-cli thread markers --session <s> --category Test --list --limit 0` on an xpcshell per-test profile.
- Got: every assertion as both a `PASS` and a `TEST-PASS` marker (240 of each here), so 480 of the 618 rows are passes.
- Workaround: `grep -v -E "  (TEST-)?PASS  "` on the output file; `--search "-name:PASS,-name:TEST-PASS"` also works, but I only found it later.
