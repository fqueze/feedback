## Question: "at what exact time (ms) did these markers happen?"

- Command: `profiler-cli thread markers --session <s> --search test_HeapSnapshot_takeCensus_08 --list --limit 0`
- Expected: start times precise enough to order events that are 10-200 ms apart (a `ps` at 889.754 s vs one at 889.932 s vs the failure at 890.075 s).
- Got: `t=14m50s` for all of them, rounded to whole seconds, so the text output cannot order events within a second.
- Workaround: `--json` and read `start` (ms) with a Python script. The text list could show `t=889.754s` (or `14m49.754s`) when the view spans more than a few seconds.

## Question: "which processes named X does each `get_process_list` marker list, and when did a pid change name?"

- Command: `profiler-cli thread markers --session <s> --search get_process_list --list --limit 0 --json` (73 MB for one job), then a Python script to parse the Python-literal lists in the message.
- Expected: some way to search inside long marker payloads and show only the matching part, e.g. `--search 'xpcshell0'` highlighting or excerpting the match within the message instead of truncating from the start.
- Got: the label is cut after ~150 characters, always before the test_runner entries, so the text output never shows the part that matched.
- Workaround: `marker info m-N | grep -o "\[24701, '[^']*'"` for single markers, or the `--json` script for all of them.

## Question (review): "the test markers of this one test, and nothing else"

- Command: `profiler-cli thread markers --session <s> --search "name:test,test_bug380398.js" --list --limit 0`
- Expected: AND, like the comma in `thread samples --search` ("Comma joins multiple terms with AND").
- Got: OR. Every `test` marker in the job was listed (762 KB of output). `thread markers --search` ORs positive terms, the opposite of the samples commands.
- Workaround: `--search test_bug380398.js --json` and filtering on `name == "test"` in Python. The same separator meaning the same thing in both commands, or an explicit AND syntax for markers, would have avoided this.
