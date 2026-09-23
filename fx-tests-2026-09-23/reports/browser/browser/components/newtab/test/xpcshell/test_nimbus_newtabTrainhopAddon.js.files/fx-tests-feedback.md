## Question: "does this one failure mode fail only in the parallel phase?"

- Command: `fx-tests test browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon.js --executions --issue 4`
- Expected: the parallel / sequential breakdown restricted to the failures behind Issues row 4 (a test with a timeout mode and an assertion mode has two different answers).
- Got: `--issue` is only honoured with `--task-ids`; `--executions` prints the breakdown for all failure modes together, so "2156 of 2231 failures are parallel" cannot be attributed to either mode.
- Workaround: `fx-tests task <id>` on individual jobs of that mode ("failed only in the parallel phase").
