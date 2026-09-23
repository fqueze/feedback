# profiler-cli feedback (browser_chat_contextmenu.js)

## Searching for a DOMEvent by its displayed text finds nothing

- Command: `profiler-cli profile markers --search "contextmenu - document" --session browser_chat_contextmenu.js-2`
- Expected: the `DOMEvent` marker the list output displays as `contextmenu - document`.
- Got: `No markers match the specified filters (searched 18 threads).`
- Workaround: `--search "DOMEvent,contextmenu"` and grep the output for `contextmenu -`.
- The row label is built from `eventType` and the target, so the text a user copies from the
  output is not a searchable string. Matching the rendered label too, or saying in the "no
  match" message that labels are not searched, would have saved a round trip.

## Default session directory is not writable in a sandbox

- Command: `profiler-cli load <taskcluster URL> --session browser_chat_contextmenu.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint
  to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every
  call (and for `profile-link.py`, which reads the same variable). The hint was good; only
  noting that it has to be repeated on every command, like the owner variable.
