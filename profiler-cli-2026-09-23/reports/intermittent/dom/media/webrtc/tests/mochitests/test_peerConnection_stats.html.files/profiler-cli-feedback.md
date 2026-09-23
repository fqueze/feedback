## Marker times are only printed to the second in text output

Question: "at what time, to the millisecond, did this TEST-UNEXPECTED-FAIL and the stat dump before it happen?" (for citing observations as `t=<s>`)

Command: `profiler-cli marker info m-3137 --session <s>` and `profiler-cli thread markers --search "same timeline" --list`
Expected: a start time with ms precision, e.g. `t=348.945s`.
Got: `Time: 5m49s (instant)` / `t=5m49s`; several markers 100 ms apart all print the same time.
Workaround: `marker info ... --json` and read `.markers[].start` (ms).

## (review) Full payload of every marker matching a search, in one call

Question: "what are the `timestamp`/`remoteTimestamp` values in each `remote-outbound-rtp.timestamp is allowed` stat dump, with ms times and marker indices?"
Command: `profiler-cli thread markers --search "remote-outbound-rtp.timestamp is allowed" --list --limit 0`, then `marker info <handles> --json`.
Expected: a way to get each match's full message, ms start and `markerIndex` from the search itself (e.g. `thread markers --search X --json` or `--full`).
Got: `--list` gives handles and second-rounded times only; I had to scrape handles with grep, feed them to `marker info --json`, and parse `fields[0].value` in Python.
Workaround: that pipeline.
