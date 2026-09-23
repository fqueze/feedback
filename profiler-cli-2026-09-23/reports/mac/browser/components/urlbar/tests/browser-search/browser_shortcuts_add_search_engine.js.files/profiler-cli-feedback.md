## `marker info` prints minute-level times past 60 s

- Command: `profiler-cli marker info m-21 m-22 m-49 m-50 --session <s>` on a 2m39s profile
- Expected: each marker's time to the millisecond, as `thread markers --list` does under 60 s (`t=2.077s`)
- Got: `Time: 1m54s (instant)` for all four, which cannot order markers a few ms apart; `thread markers --list` shows `t=1m54s` too
- Workaround: `--json` and read `.markers[].start`
- Question it could not answer: "in which order did these two instants happen, and how far apart?"


## `marker info --json` changes shape with the number of handles (review)

- Command: `profiler-cli marker info m-110 --json --session <s>`, after `marker info m-65 m-45 … --json` in the same session
- Expected: the same shape either way, so one script reads both
- Got: several handles give `{"markers": [ … ]}`; one handle gives the bare marker object, so the script iterating `.markers` crashed on the string keys
- Workaround: branch on the shape, or always pass two handles
- Question it could not answer: "what is the `markerIndex` of this one marker?", which checking a report's `marker=N` link needs for every link
