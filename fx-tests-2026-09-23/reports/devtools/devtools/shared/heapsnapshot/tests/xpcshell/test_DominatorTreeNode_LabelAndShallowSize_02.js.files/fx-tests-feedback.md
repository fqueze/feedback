## `errors --message` does not match numbers, silently

- Command: `fx-tests errors --harness xpcshell --message "2147009284" --day 2026-09-10` (also `--message "0x80073cfc"`)
- Expected: the `Failed to launch tab subprocess @CreateProcess (Error:-2147009284)` C++ warnings of task PyUxuOpdQj2b3T3XyQOKpg.0, which ran that day.
- Got: "No markers matched." Messages are stored normalized (`Error:-<num>`), so any numeric substring (an HRESULT, an error code) can never match, and nothing says so.
- Workaround: `--message "Failed to launch"`. Suggest matching against the raw text too, or warning when the search term contains digits that normalization replaces.

## `test --bugs` prints no bug section at all when none is found

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTreeNode_LabelAndShallowSize_02.js --bugs`
- Expected: a "Bugs: none" line.
- Got: the same output as without `--bugs`, so I could not tell "no bug" from "flag ignored".
- Workaround: Bugzilla quicksearch via REST.

## Question: "did this test really time out, or did its process never start?"

- Command: `fx-tests task PyUxuOpdQj2b3T3XyQOKpg.0 --profiles`
- Got: 1152 rows `TIMEOUT — Test timed out`. The job-wide cause (xpcshell CreateProcess raising `PermissionError: [WinError 5] Access is denied` from t=52s, followed 45 s later by 1149 `not killing -- proc or pid unknown` INFO lines) only shows in the resource-usage profile.
- Could show: when most of a job's tests fail, a job-level line such as "1149 of 1152 timeouts had no process (`not killing -- proc or pid unknown`)", or the traceback the harness logs after "Following exceptions were raised:".
