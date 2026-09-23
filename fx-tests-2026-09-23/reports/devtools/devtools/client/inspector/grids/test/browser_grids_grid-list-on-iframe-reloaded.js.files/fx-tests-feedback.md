## `fx-tests test` Issues list prints each stack-bearing message in full

- Command: `fx-tests test devtools/client/inspector/grids/test/browser_grids_grid-list-on-iframe-reloaded.js` (also with `--history`, `--task-ids`, `--bugs`)
- Expected: one or two lines per issue, as the width-cut rule in `guide` suggests.
- Got: 34 KB of output; issues 3–12 (`uncaught rejection: Connection closed ... getFragments`) each print their full 60-line request stack, and "↑ same as 3, but" variants reprint the stack from the first differing frame onward. Every flag re-prints the whole block, so `--history` needs a filter to find the history table.
- Workaround: `COLUMNS=250` and `rg -v` on the stack-frame lines.

## Question: "how does each failure mode trend over time?"

- Commands: `fx-tests test <path> --task-ids --limit 0 --issue <n>` for n = 1..3, then awk over the dated task list.
- Could have shown: per-issue daily counts in `--history` (here the three modes have disjoint lifetimes: `Connection closed` 09-02..09-09, the a11y check 09-02..09-10, `The checkbox is checked` 09-01..09-02 then 09-11 onward), which the aggregate history hides.

## `--bugs` prints no bug section

- Command: `fx-tests test devtools/client/inspector/grids/test/browser_grids_grid-list-on-iframe-reloaded.js --bugs`
- Expected: the bugs naming the test (2069100, 1894669 both have it in their summary).
- Got: the same output as without `--bugs`, no bug list.
- Workaround: took the bug numbers from the task.

## A `TEST-KNOWN-FAIL` is counted as a run's first failure

- Command: `fx-tests test devtools/client/inspector/grids/test/browser_grids_grid-list-on-iframe-reloaded.js` (Issues list), then `fx-tests task KT46PfwlTlmoI7lb4RstnA --messages`
- Expected: issue 1, `handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: input` (140x, the largest), to be a failure.
- Got: in the per-test profile of KT46PfwlTlmoI7lb4RstnA that message is a `TEST-KNOWN-FAIL` marker (expected), and the run's real failure is `uncaught rejection: Connection closed ... getFragments`. The Issues list, the `--issue 1` task list and `task --messages` all present the known-fail as a FAIL, and as the first failure it hides the real one from the per-issue counts.
- Workaround: read the Test markers in the per-test profile to see the status.
- Question it failed to answer: "which failure mode is the most frequent?"

