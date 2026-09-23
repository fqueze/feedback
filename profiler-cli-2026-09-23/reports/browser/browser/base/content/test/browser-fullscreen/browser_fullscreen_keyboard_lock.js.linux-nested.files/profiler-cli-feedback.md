## Question: "which of two markers 1 ms apart came first?"

- Command: `profiler-cli marker info m-40 m-41 --session <s>` (a keyup DOMEvent and a ReceiveMessage, both shown as `t=11.523s`).
- Expected: enough precision to order them. Got: `Time: 11.523s - 11.523s`, rounded to the millisecond, as in `thread markers --list`.
- Workaround: `marker info ... --json` piped through python to read `start`/`end` (11522.510 vs 11523.133). Races between IPC messages are often sub-millisecond, so `marker info` could print times at microsecond precision, since it is the detail view.

## Question: "is the marker this profiler.firefox.com link selects (marker=N) the one the report quotes?" (review-browser_fullscreen_keyboard_lock.js.linux-nested)

- Command: `profiler-cli load '<link with marker=7191&thread=v>' --session <s>`, then `thread markers --list` and `marker info m-151`.
- Expected: `load` to report which handle the link's `marker=N` selects, or `marker info` / `thread markers --list` to show each marker's index. Got: neither shows it; the index is only in `marker info --json` (`markerIndex`).
- Workaround: a loop over ~60 handles, `marker info <m> --json | python3` to print `markerIndex`, name and start. Printing the index in `marker info`, or a `marker find --index N` command, would answer it directly.
