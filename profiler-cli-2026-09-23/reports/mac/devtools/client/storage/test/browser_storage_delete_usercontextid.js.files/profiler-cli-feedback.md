## Question: in which order, to the tenth of a millisecond, did these markers on two threads happen?

- Command: `profiler-cli thread markers --session S --list --search "debounce,getStoreObjects,single-store-update,..."`, then `profiler-cli marker info m-N`.
- Expected: start (and end) times precise enough to order events that are 20-500 µs apart, across two threads (a timer in the parent and one in a content process released 21-48 µs apart; an RDP reply landing 0.1 ms before a failure).
- Got: the list prints `t=41.984s` (ms resolution) and `marker info` prints `Time: 41.645s - 41.645s`, so a dozen markers all read `t=41.984s`. The duration column is precise, the start is not.
- Workaround: `marker info m-N --json` per marker, reading `start`/`end` in a Python one-liner (13 calls per profile).
- What would have answered it: sub-ms start times in `--list` when the view is zoomed below a second, or a `--precise` / `--time-unit ms` flag; `marker info` printing start/end in ms with 3 decimals.

## Question: the runnables on one thread in a 150 ms window

- Command: `profiler-cli thread markers --session S --list --limit 0 --search "name:Runnable" --json` without a `zoom push` first (my mistake), on a parent main thread with ~1M markers.
- Expected: an error or a warning about the size, as the brief warns.
- Got: no output for >120 s; had to kill it. With `zoom push 41.94,42.113` first it answered in seconds.
- What would have helped: a `--range START,END` on `thread markers` (one command instead of push/list/pop), and a guard when `--limit 0` would return hundreds of thousands of rows.

## Question (review): how many samples does t-0 have in a 158 ms window?

- Command: `profiler-cli zoom push 41.9547,42.1127` then `profiler-cli thread samples --include-idle`, on `Qet-3KQqS7yyZdEMle120A`'s `profile_browser_storage_delete_usercontextid.js.json`, t-0, in two sessions: one loaded from the report's `profiler.firefox.com/from-url/...marker-table/?marker=975321&thread=0&v=17` link, one from the raw Taskcluster URL.
- Expected: the same count in both, as both print `View: ts-…→ts-… (158.00ms)`.
- Got: `3 running samples` in the link-loaded session, `4 running samples` in the raw-URL one.
- Workaround: none; the report says 3–4.
- What would have answered it: the sample count in `thread info` under a zoom (it prints the full-thread count only), and the same count whichever way the profile was loaded.
