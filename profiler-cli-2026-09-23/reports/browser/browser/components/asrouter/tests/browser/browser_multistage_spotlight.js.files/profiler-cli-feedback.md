## Question: the exact time of a marker, to the millisecond

- Command: `profiler-cli marker info m-11 --session <s>` and `thread markers --list`
- Expected: a timestamp precise enough to order events a few ms apart and to quote in a report (e.g. `t=116.772s`).
- Got: `Time: 1m57s` / `t=1m57s`, rounded to the second once past 1 minute.
- Workaround: `marker info ... --json` and read `start`. The list and `marker info` could print ms (`1m56.772s`).

## Question (review): was this window still open at time t?

- Command: `profiler-cli screenshots --at 62.5 -o <dir> --session <s>`
- Expected: which windows exist at t, or at least each window's close time, so "window 54 still showed a callout at t=62" can be told apart from "window 54's last frame was at t=46".
- Got: the latest frame per window id, including win 3 from t=1.890 and win 54 from t=46.554, with no sign of whether either window was still open.
- Workaround: none from screenshots. The report had to mark "the callout was still showing" as inferred.
