## WindowProc lParam shown as a thousands-separated number

- Command: `profiler-cli marker info m-1510 --session browser_fullscreen_tab_preview.js-1` (a `WindowProc` marker for `WM_MOUSEMOVE`)
- Expected: the mouse position the message carries, e.g. `x=1000, y=10`, or at least the raw value in hex.
- Got: `lParam: 656,360` — the packed LPARAM formatted as a decimal with a thousands separator, which reads like a coordinate pair (656, 360) and is not one.
- Workaround: decoded by hand, 656360 = 0x000A03E8 → x=1000, y=10. For WM_*MOUSE*/WM_NCHITTEST, decoding LOWORD/HIWORD (signed) would answer "where was the real cursor" directly.

## Is marker B nested inside marker A? (review-browser_fullscreen_tab_preview.js)

- Question: did the `WM_MOUSELEAVE` run inside the `WM_NCMOUSEMOVE` handler, right after its nested `WM_MOUSEMOVE` returned? This is the question that decided the review.
- Command: `profiler-cli marker info m-18 m-19 m-20 --session review-browser_fullscreen_tab_preview.js-1`
- Expected: start/end precise enough to compare intervals, or a "contained in / contains" line for same-thread interval markers.
- Got: `Time: 4.209s - 4.211s (1.333ms)`, rounded to ms, so a 39 µs gap and a 14 µs containment could not be seen.
- Workaround: `marker info ... --json` and a Python one-liner printing `start`/`end`.
