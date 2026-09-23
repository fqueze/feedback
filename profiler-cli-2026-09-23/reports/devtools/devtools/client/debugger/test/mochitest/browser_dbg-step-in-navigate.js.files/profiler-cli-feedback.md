## Which network request failed, and with what status (browser_dbg-step-in-navigate.js)

- Command: `profiler-cli thread network --session <s> --sort start --limit 0` (and `--json`)
- Expected: each request's final status (`requestStatus`, e.g. `NS_ERROR_ABORT`), response status and HTTP version, plus the m-N handle, so a failed/aborted load stands out; ideally a `--failed` / `--status` filter.
- Got: text shows `???  cache=Unresolved  size=1.3KB` with no request status; `--json` rows have `handle: None` and `requestStatus: None`, although the marker's payload has `requestStatus: NS_ERROR_ABORT`.
- Workaround: `thread markers --category Network --search name:Load --list --limit 0 --json` and a script over `data.requestStatus` / `data.responseEnd`.

## Time since the previous network activity on the same host

- Question: "how long had the connection to example.com been idle when this request was sent?" (it was 30.01 s, the HTTP/3 idle timeout).
- Command: the same `--json` script, sorting non-cache loads and subtracting the previous `responseEnd` from this `requestStart`.
- What could have shown it: `thread network --sort start` listing `requestStart`/`responseEnd`, the HTTP version, and the idle gap since the previous request to the same origin.

## `marker info --json` on a mixed handle list

- Command: `profiler-cli marker info <many m-N> --json`
- Expected: a uniform record shape.
- Got: tracing-type markers (DOMContentLoaded, Load, Unload in category Network) have no `rawFields` key, so a script reading `rawFields` crashed (KeyError).
- Workaround: read `data` from `thread markers --list --json` instead.
