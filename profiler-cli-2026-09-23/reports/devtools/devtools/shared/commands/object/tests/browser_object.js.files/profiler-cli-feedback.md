## Question: in what order did a few events on two threads happen, within the same millisecond?

- Command: `profiler-cli marker info m-520 m-581 m-669 m-521 m-592 --session browser_object.js-1` (and `thread markers --list`).
- Expected: start/end precise enough to order a main-thread wake-up against a worker thread's start and a dispatch 8 µs later.
- Got: `Time: 3.366s - 3.371s (4.627ms)`, `t=3.371s` — start/end rounded to 1 ms, so four events all read `3.371s`/`3.372s`. Only the duration has sub-ms precision.
- Workaround: `marker info <m> --json | python3 -c ...` to read `start`/`end`, one marker at a time.
- What would have answered it: print start/end with µs precision (e.g. `3370.565ms`) when the duration is under a few ms, or when the zoom range is under ~100 ms; or a `--precise` flag on `marker info` and `thread markers --list`.

## Review (review-browser_object.js): same µs-ordering question again, and IPCOut rows with negative durations

- Command: `profiler-cli thread markers --list --limit 0 --session review-browser_object.js-1`, zoomed on 3.369–3.3745 s.
- Got: every row in the 5 ms window shows `t=3.370s`–`t=3.374s`. Again, `marker info --json` was the only way to order a sleep, a wake and a dispatch 8 µs apart. The list also shows `IPCOut ... -391,751ns`, because the marker's `end` (3370.600) comes before its `start` (3370.992) once cross-process phases are counted. That reads as a bug, and to find the real send time you have to open the marker.
- What would have answered it: sub-ms start times in `--list` when zoomed under ~100 ms. For IPC markers, show the phase times, or at least not a negative duration.
