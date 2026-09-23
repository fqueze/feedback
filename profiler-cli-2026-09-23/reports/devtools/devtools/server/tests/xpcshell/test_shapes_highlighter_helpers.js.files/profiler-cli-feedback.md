## Marker times past 1 minute lose their milliseconds

- Question: when exactly, to the ms, did this test start relative to the first child-launch failure (both at "2m12s"/"2m13s")?
- Command: `profiler-cli marker info m-7 m-1 m-3 --session test_shapes_highlighter_helpers.js-2` (also `thread markers --list`)
- Expected: `Time: 133.456s - 178.491s` or `2m13.456s`, as markers under 1 minute get (`t=46.828s`).
- Got: `Time: 2m13s - 2m58s (45.035s)` and `2m12s (instant)`; the ~1.8 s gap between the break and the test start is invisible, and two markers 34 ms apart print the same time.
- Workaround: `marker info ... --json` and read `start` (131675.908, 133455.908, 133493.908).
