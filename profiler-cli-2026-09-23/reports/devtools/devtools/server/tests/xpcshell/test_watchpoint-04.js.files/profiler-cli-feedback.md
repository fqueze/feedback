## Several `-field:` exclusions in one `--search` returned 0 markers

- Command: `profiler-cli thread markers --session <s> --search "-name:CPU Use,-name:Memory,-name:IO,-name:NetIO,-name:Sampling Interval,-name:test,-CONSOLE_MESSAGE,-head.js" --list --limit 200` (after `zoom push 130,136`)
- Expected: the log markers in the range other than resource-usage and test markers (hundreds exist).
- Got: "0 markers (filtered from 1783)". Unclear which term excluded everything (maybe `-name:test` matching the payload `name` key, or `-name:IO` substring matching many names).
- Workaround: positive list `--search "name:ERROR,name:FAIL,name:output,name:C++,name:JavaScript"`. A note in the output saying which exclusion removed how many markers would have saved the retry.

## Question: "which tests ended just before instant T, and in what order?"

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0`
- Expected: enough time precision to order test ends around the onset of a failure.
- Got: past one minute, start times print as `t=2m13s` (1 s precision) and only the duration is given, not the end. Hundreds of markers share `t=2m13s`.
- Workaround: `--json` and a Python script to compute start/end in ms. Printing ms precision (e.g. `t=2m13.412s`) and an end time in `--list`, or a `--overlapping <t>` filter, would have answered it.
