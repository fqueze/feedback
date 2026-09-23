## `thread markers --list` and `marker info` print times at 1 s resolution

- Command: `profiler-cli zoom push 80.7,93.5 --session S; profiler-cli thread markers --thread t-112 --list --limit 0 --session S` and `profiler-cli marker info m-36 --session S`
- Expected: timestamps precise enough to order events a few hundred microseconds apart (the zoom is 12 s wide; the events of interest are 0.1 ms apart).
- Got: every row `t=1m21s`; `marker info` says `Time: 1m33s`.
- Workaround: `--json` and print `start/1000` with a script, for every ordering question.

## Question: which IPC messages did this process send, and to which process?

- Command: `profiler-cli thread markers --thread t-112 --search "IPCOut,IPCIn" --list --session S`
- Expected: the message type (`PBrowser::Msg_RealTouchEvent`, `PAPZCTreeManager::Msg_SetTargetAPZC`, ...) and the other process on each row.
- Got: rows with an empty description; the type and pid are only in `marker info m-N`, one call per marker.
- Workaround: a loop of `marker info` calls, grepping `Type:` and `Other Pid:`.

## `--search` does not match the label the list prints

- Command: `profiler-cli thread markers --thread t-0 --search "webfont.per_page" --list --session S`
- Expected: the `Counter::add` markers the list prints as `webfont.per_page : 0`.
- Got: no markers. `--search webfont` finds them (the payload is `cat=webfont`, `id=per_page`).
- Workaround: search one field's value, then filter the JSON by name.
