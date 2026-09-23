## Which document was each Reflow marker for?

- Command: `profiler-cli thread markers --session <s> --search "name:Reflow" --list --limit 0` on the parent main thread (per-test profile of task fekXaTt0SIqfZynP6Knxfg).
- Question: between the toolbox being inserted into browser.xhtml and the test's click, was browser.xhtml reflowed, or only the toolbox and inspector documents?
- Expected: each row to say which document it reflowed (the payload's `innerWindowID`, resolved to its page URL as the sample label `Reflow chrome://browser/content/browser.xhtml` does).
- Got: every row is just `Reflow (sync)` with a time and a duration; 73 rows from 5 documents look identical.
- Workaround: `--json` piped into a Python script to print `data.innerWindowID` per marker, then `marker stack` on the candidates.

## What was the machine's CPU use around one moment? (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push 17.0,19.3`.
- Question: was the machine saturated when the Renderer thread hung and when the click was sent?
- Expected: the `cpuPercent` of each `CPU Use` marker in the list, or a summary (min/avg/max) over the zoomed range.
- Got: rows with only a time and `100ms`, no value.
- Workaround: `--json` and a script printing `data.cpuPercent`.
