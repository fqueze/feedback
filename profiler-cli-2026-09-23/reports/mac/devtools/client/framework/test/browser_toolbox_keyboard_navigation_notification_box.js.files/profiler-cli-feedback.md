## Marker times in `--list` output are rounded to the second

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_toolbox_keyboard_navigation_notification_box --list --limit 0`
- Expected: each row's start time precise enough to order the test's steps against other markers (ms, as in the marker table of the profiler UI).
- Got: every row shows `t=1m17s`; the 26 test-log markers span 76534–77320 ms and cannot be ordered or compared with the `requestIdleCallback`/`setCanRender` markers from the text.
- Workaround: `--json` and a Python one-liner printing `flatMarkers[].start`. Question it could not answer: "at what ms did each line of the test's log happen". Same for every other `--list` in a zoomed 40 ms range: all rows read `t=1m17s`.

## No way to find markers by a frame in their stack

- Question: "which markers between t=76813 and the failure have `setCanRender` (or `requestIdleCallback handler`) in their stack".
- Command used: `thread markers --has-stack --list --limit 0 --json`, then one `profiler-cli marker stack m-N` per marker in a shell loop (249 + 220 + 537 calls) grepping the output.
- Expected: something like `thread markers --stack-search setCanRender`, or `marker stack` over a handle range, to answer in one call.
- Also a trap: my first pass truncated each stack to its first few devtools frames and missed nothing only by luck; `setCanRender` sits at depth 18 under React frames.

## (review) Session named as the brief asks exceeds the socket path limit

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <url> --session review-browser_toolbox_keyboard_navigation_notification_box.js-1`
- Expected: a session named `review-<report name>-1`, as the review brief asks.
- Got: `The Unix socket path for this session is 110 bytes, over this platform's 103-byte limit`, and the error suggests `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`: the directory already in use.
- Workaround: `--session review-kbnav-notif-1`. The error could suggest a shorter session name when the directory is already the one it would suggest; long test file names hit this every time.

## (review) `marker info --json` cuts stacks at 20 frames

- Command: `profiler-cli marker info m-a m-b ... --json` over the 247 stack-bearing markers of a range.
- Expected: each marker's full stack, to answer "does any marker in this range have `setCanRender` in its stack" in one call.
- Got: 49 of 247 stacks with `"truncated": true` at 20 frames, and no option to get more; `setCanRender` sits at depth 18 and its caller `requestIdleCallback handler` at 22.
- Workaround: `marker stack` once per truncated handle. A `--full-stacks` flag, or the frame search asked for above, would answer it in one call.

## (review) profile-link.py refuses a session loaded from a profiler link

- Command: `profile-link.py --session review-kbnav-notif-1 --thread t-0 --range 76.843,76.8445 --panel marker-chart --search Awake`
- Expected: a link, from a session loaded as the review brief says ("`profiler-cli load <link>` opens it at its thread").
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: stop both sessions and reload each from its Taskcluster URL (two extra 0.6 GB loads, and every m-N handle changes). Either the review brief should say to load the raw URL when new links are needed, or the script could take the artifact URL out of the `from-url/` link.
- Also: a session loaded from the raw URL starts on `t-130 (GeckoMain, https://example.net)`, not the parent main thread; one loaded from the profiler link starts on `t-0`.
