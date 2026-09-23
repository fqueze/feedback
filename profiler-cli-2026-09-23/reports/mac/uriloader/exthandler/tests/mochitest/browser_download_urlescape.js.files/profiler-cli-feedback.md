## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_download_urlescape.js profiler-cli load <taskcluster URL> --session browser_download_urlescape.js-1`
- Expected: session loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session` on the next call.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. Falling back to `$TMPDIR` when the home default is not writable would avoid it.

## Ordering markers within one millisecond

- Question: "did `ImageDocument::DefaultCheckOverflowing` run before or after `PBrowser::Msg_UpdateDimensions` in the content process?"
- Command: `profiler-cli thread markers --list --limit 0 --search "DOMEvent,UpdateDimensions,TitleChange,DefaultCheckOverflowing" --session browser_download_urlescape.js-1` on the content main thread
- Expected: start times precise enough to order the rows.
- Got: every row between the two reads `t=15.149s`; the order in the list was the only hint, and rows sharing a millisecond are not obviously sorted by start.
- Workaround: `--json` and a Python one-liner printing `start` in ms (15148.724 vs 15149.219). A sub-millisecond `t=` when several rows in the listing share the same millisecond, or a `--precision` flag, would have answered it.

## `--list --json` leaves `description` empty in one session and fills it in another

- Command: `profiler-cli thread markers --list --limit 0 --search "DOMEvent,UpdateDimensions,TitleChange,DefaultCheckOverflowing" --json --session browser_download_urlescape.js-4` (content main thread t-118)
- Expected: `description` holding the event type and target / runnable name, as it did for the same command in session `browser_download_urlescape.js-1`.
- Got: an empty `description` on every row, so the JSON gave times without saying which marker was which.
- Workaround: `marker info m-11 m-12 m-19 ...` on each handle. One extra call.

## The session header gives the selected thread the wrong process name (review)

- Command: `profiler-cli load "<profiler link to I-MUoLJ8SJKZKUSKvQbeHA, thread=zf>" --session review-browser_download_urlescape.js-2`, then `status` and `thread markers ...`
- Expected: one name for t-111.
- Got: `status` and every `[Thread: ...]` header read `t-111 (GeckoMain, file:// Content (8/27))`, but the `Markers in thread` line reads `t-111 (https://example.com (40/40))`. `thread list` shows t-111 as `https://example.com`, pid 2848, and `file:// Content` is t-102. The same happened in H9P1zaIHTJ6f_9eM7KezhA for t-106 (`file:// Content (6/28)` against `https://example.com (40/46)`). For a moment it looked as if the report's link opened the wrong process.
- Workaround: `thread list` to check which name is right. One extra call per profile.
