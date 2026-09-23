## Question: which document (window) was this DOM event dispatched in?

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session <s>`
- Expected: some way to see, per row, which document/window a DOMEvent (and SetNeedStyleFlush, DoFlushPendingNotifications) belongs to. In a mochitest the harness page and the test iframe share one content process main thread, so `pointerdown - html@10ec083d0` does not say whether the event reached the test's document or the harness page, and that was the whole diagnosis.
- Got: the list shows `eventType - target@address` only. The payload's `innerWindowID` is shown by `marker info`, one marker at a time, and not by `--list`. `--search innerWindowID:15032385537` matched nothing (0 markers), although `marker info` shows that exact value on hundreds of markers.
- Workaround: `--list --limit 0 --json` piped to a Python script that groups `flatMarkers[].data.innerWindowID`, then matched the id to the `DocumentLoad` marker carrying the same id to get the document URL.
- What would have answered it: an `innerWindowID` column (or better, the document URL resolved from the matching `DocumentLoad`/`Load` marker) in `--list`, and `--search innerWindowID:<n>` matching numeric payload fields.

## Default session directory not writable in a sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message suggested `PROFILER_CLI_SESSION_DIR`, which worked.
- Cost: one failed load. The suggestion in the message was good; worth mentioning in `guide`.
