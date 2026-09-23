## Marker times lose millisecond resolution after t=60s

- Command: `profiler-cli thread markers --search "load - iframe,mouseover,mouseenter,RealMouseMove,DispatchSynthMouse" --list --limit 0 --session test_mouse_enterleave_iframe.html-3` (zoomed to 64.1,64.4 in a 6-minute profile)
- Question: in what order, and how many ms apart, did the iframe's load, the native mouse move and the embedder's DOM events happen, inside a 15 ms window at t=64.2s?
- Expected: times like `t=64.247s`, as printed below t=60s (`t=48.727s`).
- Got: every row printed `t=1m4s`, so the order within the window could not be read.
- Workaround: `--json` and a script printing `start` in ms (`artifacts/.../test_mouse_enterleave_iframe.html.files/flat.py`). The default output could keep ms once past a minute (`t=1m4.247s`).

## IPC rows in `--list` do not say which message or which process

- Command: `profiler-cli thread markers --list --limit 0` on the parent main thread zoomed to 48.715,48.76 (and `--search Mouse`).
- Question: which process did the parent send this mouse event to?
- Expected: the IPC row to show its message type and peer, e.g. `IPCOut PBrowser::Msg_RealMouseMoveEvent → http://mochi.test (pid 1854)`.
- Got: bare `IPCOut` / `IPCIn` rows with an empty description; the message type and peer only appear in `marker info`, one marker at a time.
- Workaround: `marker info m-A m-B ...` on each candidate, or `--json` and reading `fields` (`messageType`, `recvThreadName`, `otherPid`).

## Session directory not writable in a sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session test_mouse_enterleave_iframe.html-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message said what to do.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. Cost one retry; noted because every agent in a sandboxed fleet will hit it.

## The test's log is not on the parent main thread in xorig configs

- Command: `profiler-cli thread markers --category Test --search test_mouse_enterleave_iframe --list --limit 0` with t-0 (parent GeckoMain) selected.
- Got: 0 markers. In `mochitest-plain-xorig`, the TEST-* markers are on the `http://mochi.xorigin-test` content process main thread, and its INFO markers carry no text (`[(empty)] INFO: (empty)`).
- Workaround: `profiler-cli profile markers --search <test file>` found the right thread. A hint in `profile info` or in the empty-result message ("TEST markers for this test are on t-167") would have saved the step.

## (review) One chronological list across several threads

- Command: `profiler-cli thread markers --list --limit 0 --session review-test_mouse_enterleave_iframe.html-2` run once per thread (t-0, t-8, t-14, t-18), zoomed to 3.48,3.54, each with `--json`, then merged by a script.
- Question: in what order did the iframe's load, its paint unsuppression, its first display lists, the embedder's display list, the APZ hit test and the scene builds happen, across parent, embedder, iframe and compositor threads, inside 60 ms?
- Expected: something like `profile markers --threads t-0,t-8,t-14,t-18 --search ... --list` printing one interleaved list with a thread column and ms times.
- Got: `thread markers` is per thread; `profile markers --search` says which threads have a marker but is not a timeline.
- Workaround: `--json` per thread, merged and sorted on `start` by a script (and `messageType`/`otherPid` read from `fields`, the IPC-rows issue above).
