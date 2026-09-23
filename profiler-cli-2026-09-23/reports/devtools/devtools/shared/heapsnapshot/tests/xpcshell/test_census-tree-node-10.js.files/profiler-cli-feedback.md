## Question: "at what exact time (ms) did each of these markers happen?"

- Command: `profiler-cli thread markers --session <s> --search test_census-tree-node-10 --list --limit 0`
- Expected: start times precise enough to order harness log lines against each other and against logcat (ms, or at least 0.1 s).
- Got: `t=3m51s`, `t=3m55s`, `t=4m37s` — rounded to the second once the profile is past one minute. Two markers 165 ms apart both read `t=4m37s`.
- Workaround: `--json` and a script reading `flatMarkers[].start`. The text list could print `t=231.121s` (or `3m51.121s`) in `--list` mode.

## Question: "which processes named `…test_runner:xpcshellN` does each `get_process_list` DEBUG marker list?"

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list` (after `zoom push`)
- Expected: a way to see the relevant part of a 30 KB payload.
- Got: each label is the full ps dump (~340 processes); the interesting entries are at arbitrary offsets, and the list output cuts or floods.
- Workaround: `--json`, then `ast.literal_eval` of the label and a filter in Python. A `--grep`-style option that prints only the matching fragment of long labels would have answered it.

## Review (review-test_census-tree-node-10.js): both questions above cost time again

- Same two commands, same result: `t=3m55s`-style times in `--list` (had to use `--json` for ms to align with logcat), and the `get_process_list` payloads (had to `--json` + regex to get the `xpcshellN` pids per snapshot in 225–290 s).
