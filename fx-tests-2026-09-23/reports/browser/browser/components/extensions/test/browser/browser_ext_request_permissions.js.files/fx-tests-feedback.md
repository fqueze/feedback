## `fx-tests test --task-ids` gives a chunk number that disagrees with the task's own name

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_request_permissions.js --task-ids --limit 0` (and its `--json`, field `chunk`)
- Expected: `IT0S_eCQRJ-SkMnxZ3h6jA.0  test-macosx1500-aarch64/opt-mochitest-browser-chrome-7`, as `fx-tests task IT0S_eCQRJ-SkMnxZ3h6jA` and the Taskcluster task metadata (`test-macosx1500-aarch64/opt-mochitest-browser-chrome-7`) both say.
- Got: `...-chrome-1` (`"chunk": 1`).
- Workaround: trust `fx-tests task <id>` for the job name.

## `fx-tests test <path> --bugs` prints nothing about bugs when none is found

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_request_permissions.js --bugs`
- Expected: a Bugs section, or a line saying no bug names this test.
- Got: exactly the output without `--bugs`; no Bugs section and no "none found" line, so "no bug" and "flag ignored / lookup failed" look the same.
- Workaround: none; took it as no bug.
