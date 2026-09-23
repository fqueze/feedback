## Session name derived from a long report name exceeds the Unix socket path limit

- Command: `profiler-cli load <taskcluster URL> --session browser_ext_webNavigation_onCreatedNavigationTarget_named_window.js-ru1`
- Expected: a session named after the report, as the brief asks (`<report name>-1`).
- Got: `Error: The Unix socket path for this session is 105 bytes, over this platform's 103-byte limit`.
- Workaround: a shorter session name (`wnnw-ru1`), keeping the full report name as `PROFILER_CLI_SESSION_OWNER`. Hashing or truncating the socket file name (the session id could still be long) would avoid this for long test file names.

## The socket-length error came before the unwritable-directory error

- Command: same as above, with the default session directory.
- Got first: the 103-byte socket limit error. After shortening the name: `Cannot create the profiler-cli session directory /Users/florian/.profiler-cli ... EPERM`.
- Expected: the directory problem reported first, since it is the one that blocks any name; the first message sent me to rename the session for nothing.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call, via a wrapper script.

## Question: "which document keeps ticking the refresh driver, and since when?"

- Command: `profiler-cli thread markers --search 'name:RefreshDriverTick waiting for paint' --group-by field:innerWindowID` (on the WebExtensions GeckoMain).
- Expected: per group, the first and last time and the largest gap, plus the URL of the document behind the `innerWindowID` (the profile's `pages` table has it).
- Got: counts and example handles only. To get the span and the gaps I had to script over `--list --limit 0 --json`, and to get the URL I searched `nsRefreshDriver` markers ("initial timer start <url>") for the same `innerWindowID`.
- What would have answered it: a first/last/max-gap column in grouped output, and the page URL shown next to any `innerWindowID` value.
