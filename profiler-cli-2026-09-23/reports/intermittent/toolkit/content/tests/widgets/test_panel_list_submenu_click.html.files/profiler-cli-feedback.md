## Question: "exactly when, to the millisecond, did each of these events happen, late in a long profile?"
- Command: `profiler-cli thread markers --session <s> --search DOMEvent,TEST-,INFO --list --limit 0` on a 21-minute profile, zoomed to 978.9–979.5 s.
- Expected: times precise enough to order and space events a millisecond apart (keydown vs keyup vs TEST-PASS).
- Got: every row printed as `t=16m19s`. Past one minute the time column drops everything below the second, so a 600 ms window reads as one instant.
- Workaround: `--json` plus a python one-liner printing `start/1000` with 3 decimals. The default output could keep milliseconds (e.g. `16m18.991s`) at any magnitude.

## Question: "what is this marker's timestamp?" via `marker info --json`
- Command: `profiler-cli marker info m-103 m-107 --json`
- Expected: each record carries its handle and start and end times, next to the name.
- Got: records had `start`, but no `handle` field (printed None), so matching records back to the requested handles relied on their order.
- Workaround: relied on the order.
