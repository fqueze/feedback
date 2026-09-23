## Question: did this window get created before or after the previous test ended?

- Command: `profiler-cli thread markers --session <s> --search "Child 9,700,..." --list --limit 0`
  and `profiler-cli marker info m-590 m-2449 --session <s>` on a resource-usage profile.
- Expected: start/end times precise enough to order markers a few ms apart past the first minute.
- Got: `t=1m4s` in the list and `Time: 1m4s - 1m7s (2.990s)` in `marker info`: past 60 s the
  sub-second part is dropped, so a DOMWindow created 8 ms after a `test` marker ended looks
  simultaneous with it.
- Workaround: `marker info ... --json` piped to python to read `start`/`end` in ms.
- What would have answered it: keep millisecond precision past one minute (e.g. `t=64.175s` or
  `t=1m4.175s`), as it already does below 60 s (`t=45.181s`).

## Question: which markers belong to child process 5472? (review, browser- session)

- Command: `profiler-cli thread markers --session <s> --search "pid5472,5,472,Completed ShutdownLeaks,leaked,name:test" --list --limit 0`
- Expected: the `DocShell`/`DOMWindow` markers whose description reads `[Child 5,472: Main Thread]`.
- Got: 1,949 markers: the comma in `5,472`, which is how the list prints the pid, split it into
  the terms `5` and `472`. The text the list shows cannot be searched back as shown.
- Workaround: `pid:5472` (the payload field), found from `marker info --json`.
- What would have answered it: print pids without a thousands separator, or accept a quoted
  term containing a comma.
