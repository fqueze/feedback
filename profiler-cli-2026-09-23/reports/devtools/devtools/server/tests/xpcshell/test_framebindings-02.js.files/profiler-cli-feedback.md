## Question: when exactly did this marker start, to the millisecond, relative to another marker?

- Command: `profiler-cli marker info m-1 m-3 m-7 --session test_framebindings-02.js-2` (and `thread markers --list`)
- Expected: start times precise enough to order markers 30 ms apart (e.g. `t=2m13.164s`).
- Got: `Time: 2m13s - 2m58s (45.026s)` and `t=2m13s` in the list; three markers within 1.5 s all print as `2m12s`/`2m13s`. Durations keep milliseconds, start times past one minute do not.
- Workaround: `marker info ... --json` and reading `start` (ms). Past t=60s the text output could keep milliseconds, like it does below one minute (`t=46.504s`).
