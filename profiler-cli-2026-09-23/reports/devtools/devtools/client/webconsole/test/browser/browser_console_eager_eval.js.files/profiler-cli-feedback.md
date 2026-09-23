## review-browser_console_eager_eval.js: `zoom push` silently misparses the times the marker list prints

- Command: `profiler-cli zoom push 23m0s,23m45s --session review-browser_console_eager_eval.js-1`
- Expected: a zoom to 23m00s–23m45s (the `t=23m21s` form `thread markers --list` prints), or an error saying the format is not accepted.
- Got: `Pushed view range: ts-1 (23s) to ts-1 (23s) (duration: 0s)`, a zero-length zoom at 23 s, with no warning. The following `thread markers --list` showed 5 unrelated markers.
- Workaround: convert to seconds by hand (`zoom push 1380,1426`).
