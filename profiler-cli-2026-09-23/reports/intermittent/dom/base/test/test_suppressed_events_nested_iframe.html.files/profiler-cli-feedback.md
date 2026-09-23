# profiler-cli feedback (test_suppressed_events_nested_iframe.html)

## Log markers print "Message: (empty)" although the message is there

- Command: `profiler-cli marker info m-44..m-52 --session test_suppressed_events_nested_iframe.html-1`
  (and `thread markers --category Test --list`, which prints `[(empty)] INFO: (empty)`)
- Expected: the test's `info()` text, e.g. `xhr open`, `xhr done`, `received: ready`.
- Got: `Level: (empty)` / `Message: (empty)` for every mochitest `INFO` (type `Log`) marker.
  `--json` shows `"value": "xhr open", "formattedValue": "(empty)"`: the value is there, the
  formatter drops it.
- Workaround: `marker info ... --json | jq '.markers[] | .fields[].value'`.
- Question it blocked: "what did the test log, and when" — the test's own log, the first thing
  the brief asks for. The default output answers nothing for `info()` lines.

## IPC markers in `--list` do not show the message type

- Command: `profiler-cli thread markers --search "RealMouse,Synthesize,mousedown,mouseup" --list --session ...`
- Expected: each `IPCOut`/`IPCIn` row to name its message, e.g. `PBrowser::Msg_RealMouseButtonEvent`.
- Got: `m-1140 IPCOut t=1m53s 299.99ms ✗` with no description; the message type is only in
  `--json` (`.data.messageType`). Rows are also printed at `t=1m53s` resolution, so several
  messages within a few ms cannot be ordered from the text output.
- Workaround: `--list --limit 0 --json | jq '.flatMarkers[] | select(.data.messageType|test(...))'`.
- Question it blocked: "did the child receive PBrowser::ChildToParentMatrix before or after the
  synthesized mousedown" — needs message names and ms-precision times together.

## DOMEvent list does not show which document the target is in

- Command: `profiler-cli thread markers --search DOMEvent --list --session ...`
- Expected: some hint of the target's document (innerWindowID or its URL), since `div@<addr>`
  alone cannot say whether a click hit the outer or the nested iframe's `<div>`.
- Got: `click - div@70be7f767100` only; `innerWindowID` is in `--json` `rawFields`/`data`, and
  mapping it to a URL means correlating with `load`/`DOMContentLoaded` markers by hand.
- Workaround: `--json | jq '... .data.innerWindowID'`, then match by load order.
- Question it blocked: "which document did the synthesized click land in".

## The test log of a mochitest-plain test is not on the parent main thread

- Command: `profiler-cli thread markers --category Test --search test_suppressed_events_nested_iframe --list --limit 0` on t-0.
- Expected (per the diagnose brief): the test's log.
- Got: 0 markers. For mochitest-plain the `TEST-*`/`INFO` markers are on the content process
  (`http://mochi.test` GeckoMain), found with `profile markers --search`.
- Workaround: `profile markers --search <test name>` to find the thread first.

## (review) `--search` does not match the text of mochitest `INFO` markers

- Command: `profiler-cli profile markers --search "received: ready" --session review-test_suppressed_events_nested_iframe.html-2`
  (also `thread markers --search ready --category Test --list` on the thread that has it)
- Expected: the `INFO` marker whose payload value is `received: ready` (it is listed by
  `thread markers --category Test --list --json`).
- Got: `No markers match the specified filters (searched 126 threads)`. Probably the same cause
  as "Message: (empty)" above: search seems to run on the empty formatted value.
- Workaround: `thread markers --category Test --list --limit 0 --json | jq` on each candidate thread.
- Question it blocked: "which thread has the test's `info()` line", in a profile where the popup
  logs from a process other than the harness.
