## Question: did the try push schedule any job on the configs a removed `skip-if` covers?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/framework/browser-toolbox/test/browser_browser_toolbox_evaluation_context.js`
- Expected: next to the per-config ran/pass/fail table, a line saying which of the test's skip conditions (or which platforms) the push did not schedule at all. For example: "no macOS job on this push; the test's skip-if covers only mac".
- Got: 7 linux/windows configs, all passing, and nothing about mac. "Passes everywhere" and "never ran where it was skipped" look the same.
- Workaround: a Python script over the Treeherder `/api/jobs/?push_id=` JSON that counts jobs per platform.

## `fx-tests test <path> --bugs` prints no Bugs section and no "none found"

- Command: `fx-tests test devtools/client/framework/browser-toolbox/test/browser_browser_toolbox_evaluation_context.js --harness mochitest --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, so I could not tell whether the flag took effect.
- Workaround: took the bug numbers from the manifest's `skip-if` comments.
