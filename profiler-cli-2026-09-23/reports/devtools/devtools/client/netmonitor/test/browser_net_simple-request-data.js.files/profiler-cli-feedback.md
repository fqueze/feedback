## Marker times in a profile longer than a minute

- Command: `profiler-cli marker info m-20 m-265 --session browser_net_simple-request-data.js-2` (a 5m9s profile), and `thread markers --list` on it.
- Expected: marker times to the millisecond, as for shorter profiles (`t=12.234s`).
- Got: `Time: 3m45s`, and `t=3m40s` for every row of the test's log, so the ordering and the 5 s `waitFor` gap between two failures cannot be read.
- Workaround: `marker info --json` and read `start` (ms). Keeping millisecond precision in `3m45.386s` form would answer it.

## `zoom push` with bare numbers read as seconds, and pushes an out-of-profile range anyway (review)

- Command: `profiler-cli zoom push 18000,98000 --session review-browser_net_simple-request-data.js-1` (a 1m39s profile), then `thread samples`.
- Expected: a range that is out of the profile refused, or bare numbers matched to the profile's scale (ms, as in link `range=` params).
- Got: a warning ("Did you mean to use milliseconds?"), but a 22h13m range was pushed, and `thread samples` then said "No samples in the current view".
- Workaround: `zoom pop`, then `zoom push 18s,98s`. Refusing a range that lies entirely outside the profile would save the round trip.
