## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_select.js profiler-cli load <taskcluster url> --session browser_select.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message itself was good and suggested `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.
- Workaround: exported `PROFILER_CLI_SESSION_DIR` on every command. The subagent brief does not mention it, so every agent here hits this once.

## Question: which thread and time a DOM event on a given element happened

- Command: `profiler-cli profile markers --session browser_select.js-2 --search "focus - select,keydown - select,A11y Event - focus,..."`
- Expected: the `DOMEvent` markers shown as `focus - select@... id="select"` and `keydown - select@...` to match, since that is the text the list prints.
- Got: no `DOMEvent` matched. The printed label is built from the fields and is not searchable as a whole. Only `A11y Event - focus` matched, through its name.
- Workaround: `thread select` on the content thread, then `thread markers --search DOMEvent --list`, reading the whole list by eye.

## Question: when did the other process send this IPC, on the profile's timeline (review-browser_select.js)

- Command: `profiler-cli marker info m-456 --session review-browser_select.js-1` (an `IPCIn` of `PDocAccessible::Msg_FocusEvent`)
- Expected: send and receive times on the same zero-based timeline as `Time:` and every other marker, to compare with the parent's refresh tick.
- Got: `Time: 10.920s - 10.922s`, but under "Other payload fields (no schema)" `sendStartTime: 10927.98…`, `recvEndTime: 10928.02…` are raw, about 7.98 ms off the displayed timeline, and nothing says so. Read at face value, the IPC looks sent 7 ms after the test check, which is the wrong ordering.
- Workaround: took the offset from the payload `endTime` against the matching `Runnable`'s `start` in `--json`. It could show the send/transfer/receive phases zero-adjusted, as `Time:` is.
