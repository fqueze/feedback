## `test --bugs` is silent when no bug names the test

- Command: `fx-tests test browser/components/asrouter/tests/browser/browser_asrouter_cfr.js --bugs`
- Expected: a line saying no bug names this test (or the bugs found).
- Got: the same output as without `--bugs`, with no Bugs section and no "none found" line, so "no bug" and "the flag did nothing" look the same.
- Workaround: grepped the output for "bug" to make sure nothing was hidden, then wrote "none".

## `task <id>` assumes run `.0` even when the failing run is `.1`

- Command: `fx-tests task ZM5Xz_B4RoGLMjyjpG6GTg --profiles` (the ID from `test --task-ids`, which lists it as `ZM5Xz_B4RoGLMjyjpG6GTg.1`)
- Expected: the failing run, or a hint naming `.1`.
- Got: exit 4, "has no profile_resource-usage.json ... `.0` is assumed", which reads as the artifacts having expired.
- Workaround: reran with `.1`. The message does say to check the retry number; it could say which runs the task has.
