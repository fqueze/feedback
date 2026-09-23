# profiler-cli feedback (browser_urlBar_zoom.js)

## Question: "in what order, and how many ms apart, did these markers happen?"

- Command: `profiler-cli thread markers --search "AsyncExecuteStatements,ZoomChange,TEST-,..." --list --limit 0 --json --session ... | python3 -c '...sort by start, print ms...'`
- Why: in an 84 s profile, `--list` prints `t=1m24s` for every marker in the test, so you cannot read their order or the 1-5 ms gaps that mattered here, such as a TextZoomChange arriving 0.16 ms after `mailto::onLocationChange`.
- Could have shown: when zoomed to a sub-second range (or always), print start times with millisecond precision.

## Question: "when does the `--list` view's Runnable end?"

- Command: `profiler-cli marker info m-63 m-701 m-41 --json` plus a script, to tell whether a DOMEvent fell inside a Runnable's interval.
- Could have shown: `--list` rows could include each interval marker's end (or start+duration at ms precision).
