## `thread markers --list` shows times past 60 s as `1m1s`

- Command: `profiler-cli thread markers --session S --category Test --search <test> --list --limit 0`
- Expected: millisecond timestamps (`t=61.757s`), as for times under a minute.
- Got: `t=1m1s` for every marker between 61 and 62 s, so the order of animationend / customizationchange / popuphidden / TypeError (all within 15 ms) was unreadable; `marker info` prints `Time: 1m1s - 1m2s` too.
- Workaround: `--json` and a script printing `start/1000` with 3 decimals.

## `--search 'FAIL —,PASS —'` matches no `test` marker

- Command: `profiler-cli thread markers --session S --category Test --search 'FAIL —,PASS —' --list`
- Expected: the per-test `test` markers, whose printed label is `FAIL — <path>`.
- Got: 0 markers. Searching for the path fragment worked.

## (review) A session named after the report does not fit the Unix socket path

- Command: `profiler-cli load <link> --session browser-review-browser_downloads_context_menu_always_open_similar_files.js-1`
- Expected: a session with the `<report name>-1` name the brief asks for.
- Got: `The Unix socket path for this session is 109 bytes, over this platform's 107-byte limit`.
- Workaround: a shorter session name (`browser-review-alwaysopen-1`). The owner name can stay long. Could the socket be named after a hash of the session id?
