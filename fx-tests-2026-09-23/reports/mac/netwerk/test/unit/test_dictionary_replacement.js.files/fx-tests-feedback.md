## The configs behind one failure mode

- Question: which platforms does each failure mode hit? (here the "null !== null" mode is 32/40 Windows, the hash-mismatch mode 21/37 macOS)
- Command: `fx-tests test netwerk/test/unit/test_dictionary_replacement.js --issue 2`
- Expected: the per-config table restricted to that issue's runs.
- Got: `--issue selects which failure the printed task IDs belong to, so it needs --task-ids`.
- Workaround: `--task-ids --limit 0 --issue N` for each issue and counting the config names by hand (a job with two runs, `×2`, has to be counted twice).
- What the output could show: a config column summary under the task list of `--task-ids --issue N` ("windows 32, macos 8"), or accept `--issue` without `--task-ids` to filter the config table.
