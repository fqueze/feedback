## Which of two markers came first, when both print the same millisecond

- Question: did the synthesized `contextmenu` DOMEvent start before or after the vsync timestamp of the next refresh tick (the `CSS transition` marker's start)? Both print as `t=8.217s`; the answer was a 31 µs difference.
- Commands: `profiler-cli thread markers --search ... --list`, `profiler-cli marker info m-58 m-213`.
- Expected: start times with enough digits to order markers that fall in the same millisecond, at least in `marker info` (e.g. `8.217227s`), or a relative offset when several handles are passed.
- Got: `Time: 8.217s - 8.222s (5.239ms)` in `marker info`, and `t=8.217s` in the list; only `--json` has `start: 8217.226542`.
- Workaround: `marker info m-58 m-213 --json | python3 -c ...` to print `start`.

## Which marker's stack contains a given function

- Question: which of the ~20 `SetNeedStyleFlush` markers in one refresh tick was the `setAttribute("fadein")` from `Tabbrowser.sys.mjs!addTab/<`?
- Commands: `profiler-cli thread markers --search name:SetNeedStyleFlush --list`, then `profiler-cli marker stack m-4502`, `m-4503`, ... one by one.
- Expected: a filter on stack content, e.g. `thread markers --stack-search addTab`, or the leaf JS frame in the `--list` row.
- Got: no way to filter markers by a frame in their stack; the list rows show only `✓` for "has a stack".
- Workaround: `marker stack` on each handle in turn until the right one appears (7 calls in one profile, 4 in another).

## (review) A session name that is too long for its socket, with a suggestion that cannot help

- Question: none; loading a profile under a session named after the report, as the brief asks.
- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <taskcluster URL> --session review-browser_tab_notes_menu.js.ineligible-tabs-timeout-1`
- Expected: the session starts (for example by hashing a long name into the socket file name), or the error suggests a change that would work: a shorter `--session`.
- Got: `The Unix socket path for this session is 104 bytes, over this platform's 103-byte limit`, then "Use a shorter session directory, for example: PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli", the directory already in use.
- Workaround: dropped `.js` from the session name.

## (review) The index of one screenshot marker

- Question: is the report's screenshot link, `marker=6293`, the screenshot at t=8.218s?
- Command: `profiler-cli marker info m-550 --json` on a `CompositorScreenshot` marker.
- Expected: `markerIndex` and the thread, with the image left out or truncated, as it already is under `rawFields`. `marker screenshot` is there for the image.
- Got: about 10 KB of output: the whole JPEG in base64 twice, as `screenshot.url` and `screenshot.base64`.
- Workaround: a Python filter that drops `fields` and `data`, which still printed the `screenshot` object.
