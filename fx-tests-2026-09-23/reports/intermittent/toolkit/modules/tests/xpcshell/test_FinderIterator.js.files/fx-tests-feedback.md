## Question: which jobs failed, per failure mode?

- Command: `fx-tests test toolkit/modules/tests/xpcshell/test_FinderIterator.js --task-ids --limit 0` (and the same with `--issue 1`)
- Expected: one list of the failing jobs, each once, labelled with its failure mode.
- Got: the "Task IDs (21 jobs)" block listed the timeout jobs, then the FAIL jobs, then the FAIL jobs a second time under repeated date headers. With `--issue 1` it printed "11 jobs" followed by the same 11 twice. Nothing on a row says which failure mode it is.
- Workaround: running `--issue 1` and `--issue 2` separately and de-duplicating by eye.
