## Question: did the try push run any job on the configs the removed `skip-if` targeted?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/framework/browser-toolbox/test/browser_browser_toolbox_take_screenshot.js`
- Expected: alongside the per-config ran/pass table, a line saying the test's former `skip-if` conditions (`os == 'mac' && os_version == '14.70'`, `os == 'mac' && os_version == '15.30' && arch == 'aarch64'`) matched no job on the push, so "21/21 passed" says nothing about the configs it was skipped on.
- Got: a table of 7 linux/windows configs, all passed. Nothing says the push had no mac job at all.
- Workaround: `treeherder-cli <rev> --json --match-filter all | jq '.jobs[].job.platform' | sort | uniq -c` (14 MB of JSON) to see there were no macosx jobs.

## `fx-tests test <path> --bugs` prints nothing about bugs when none are found

- Command: `fx-tests test devtools/client/framework/browser-toolbox/test/browser_browser_toolbox_take_screenshot.js --bugs`
- Expected: a "Bugs: none naming this test" line (or the list).
- Got: the same output as without `--bugs`; no bug section at all, so "no bug" and "flag ignored" look the same.
- Workaround: Bugzilla REST `bug?summary=<test file name>`.
