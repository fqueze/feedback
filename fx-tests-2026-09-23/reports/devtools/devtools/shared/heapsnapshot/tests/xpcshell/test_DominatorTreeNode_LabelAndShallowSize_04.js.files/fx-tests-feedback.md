## `--bugs` with no matching bug prints nothing about bugs

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTreeNode_LabelAndShallowSize_04.js --bugs`
- Expected: a "Bugs" section, saying "none name this test" when there are none.
- Got: the same output as without `--bugs`, with no Bugs section and nothing on stderr. I could not tell "no bug" from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>`.

## Question: in a job where most tests failed at once, did this test's process ever launch?

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg --profiles --limit 0` (1,154 failing tests, all "TIMEOUT").
- The answer needed a script over `profiler-cli ... --json` of the resource-usage profile: for each test, the time from its `test` marker start to its `... failed or timed out, will retry.` line. 1,149 were under 1 s (median 22 ms), which means the launch failed and the kill timer later reported a false TIMEOUT.
- What `task` could show: a per-test "ended after N ms, reported TIMEOUT at N s" column, or a group of "timed out, but finished within 1 s": those are launch failures, not timeouts.
