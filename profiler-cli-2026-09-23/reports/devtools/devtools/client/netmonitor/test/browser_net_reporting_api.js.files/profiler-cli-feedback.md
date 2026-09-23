## `thread samples` with no zoom reports a handful of samples after a zoom push/pop

- Command: `profiler-cli thread samples --session browser_net_reporting_api.js-1 --include-idle --limit 3` (after several `zoom push` / `zoom pop` pairs; `status` said "View range: Full profile").
- Expected: the parent main thread's samples over the whole 1m57s profile.
- Got: `Categories (6 running samples)`; `thread functions` likewise said `42 functions`. `zoom push 0,117.3` on the same session gave `2311 running samples`.
- Workaround: always `zoom push 0,<end>` explicitly instead of trusting the full view.

## Question: "which requests in this session are POSTs?" — `thread network` has no method

- Command: `profiler-cli thread network --session browser_net_reporting_api.js-1 --limit 0` (and `--json`).
- Expected: the HTTP method per request, or a `--method` filter. The one stuck request here was the only HTTPS POST in the session, which is a lead.
- Got: no method in text or JSON output. `thread markers --category Network --search requestMethod:POST --list` matched 0 markers although `marker info` shows `requestMethod: POST` among the raw payload fields (no schema); a bare `--search POST` matches URLs containing "post".
- Workaround: `thread markers --category Network --search "Load " --list --limit 0 --json` and a script over `flatMarkers[].data.requestMethod`.
