## Marker times lose precision in long profiles

- Command: `profiler-cli marker info m-50 m-16 --session <s>` and `thread markers --list` on a 2m10s profile.
- Expected: a timestamp precise enough to zoom on (e.g. `128.620s`).
- Got: `Time: 2m9s (instant)` for both markers, 164 ms apart; the list shows `t=2m9s` for every marker in the test.
- Workaround: `marker info --json` and read `start` (ms). Question the default output could answer: "at what time, to the millisecond, did this marker happen?"

## When did each interval marker end, to the ms? (review)

- Command: `profiler-cli thread markers --search "content-prefs.sqlite#0: waiting for clients: Transaction" --list --limit 0 --session <s>` on 82 s and 130 s profiles.
- Expected: start and end of each interval marker, to the millisecond. The question was whether the previous transaction was still queued when this one was enqueued.
- Got: `t=2m9s` for every row, and a duration but no end. Also, `marker info m-a m-b --json` records carry no `handle` field, so they can only be matched to the handles by their order.
- Workaround: `--json` and a script that adds up `start + duration` from `flatMarkers`.
