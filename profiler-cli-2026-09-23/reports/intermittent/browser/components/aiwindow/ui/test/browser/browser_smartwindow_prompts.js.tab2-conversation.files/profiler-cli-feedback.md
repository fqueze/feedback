## Question: "in what order, to the millisecond, did these markers happen?"

- Command: `profiler-cli thread markers --category Test --search browser_smartwindow_prompts.js --list --limit 0 --session <s>` (and the same with `--search MLEngine`, `--search DOMEvent` inside a 25 ms zoom).
- Expected: start times precise enough to order markers a fraction of a millisecond apart, like the `t=12.671s` form the guide shows.
- Got: in a 17-minute profile, every row reads `t=16m45s`. Inside a 25 ms zoom, dozens of markers all show the same time, so the list cannot give the order of events in a race. `marker info` also prints `Time: 16m45s`.
- Workaround: `--json` and a Python one-liner printing `start/1000` with 5 decimals. Every ordering step of this diagnosis needed that.
- What could have answered it: print times relative to the zoom start, or in seconds with ms precision (`t=1004.9477s`), once the view is shorter than a few seconds, or always in `--list` mode.

## Question: "what IPC message is this IPCIn/IPCOut row?"

- Command: `profiler-cli thread markers --search "IPCOut,IPCIn" --list --session <s>`
- Expected: the message type (for example `PMessagePort::Msg_PostMessages`) and the other process in the row.
- Got: `IPCOut IPCOut` / `IPCIn IPCIn` with no type. The type only shows in `marker info` or `--json` `fields[messageType]`.
- Workaround: `--json`, then a script reading `fields` to print messageType, otherPid and start.
- What could have answered it: put `messageType` and the other pid in the list label for IPC markers, the way DOMEvent rows show the event type.

## `--session` before the subcommand is rejected (review-browser_smartwindow_prompts.js.tab2-conversation)

- Command: `profiler-cli --session <s> zoom push 1004.94,1004.965`
- Expected: `--session` accepted as a global option, as it is on `load`'s side of the guide ("Recommended for scripting").
- Got: `error: unknown option '--session' (Did you mean --version?)`. It only works after the subcommand: `profiler-cli zoom push … --session <s>`.
- Workaround: move `--session` to the end of every command.
- What could have helped: accept `--session` as a global option in any position, or name the right position in the error.
