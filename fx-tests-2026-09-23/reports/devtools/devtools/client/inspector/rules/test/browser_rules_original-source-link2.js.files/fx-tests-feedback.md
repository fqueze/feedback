## Question: "every job on the failing push where this test failed"

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_original-source-link2.js --task-ids --limit 0`
- Expected: all the jobs where the test failed on autoland `b606af2b1dd6`.
- Got: 1 failure, one task (`NPZ-lvP8TBu_YrCw7jYOMQ.0`). `fx-tests task` shows the same test failing, same message, in 3 more jobs of that push: `Xj9Q5gtxTiyvJCkHSfANvA.0` and `J70se26ZRxu18g_ycExv8w.1` (linux asan retriggers), and `ekeOE8c2SLOHFciLgs7m3A.0` (windows11 asan devtools-chrome-6). The linux debug http3-3 job `TEewen-TSvOKPn4qoUOCpA` crashed in it too. So the headline "1 fail, 1 config" undercounted by 4x and missed a platform.
- Workaround: `curl` the Treeherder API (`jobs/?push_id=...`, then `fx-tests task` on each). It would help if `test` counted retriggers and said which jobs it could not read.

## `--bugs` with no matching bug prints nothing

- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs: none found" line.
- Got: the normal output with no bug section, the same as without the flag. I could not tell "no bug" from "flag ignored". Workaround: Bugzilla quicksearch.
