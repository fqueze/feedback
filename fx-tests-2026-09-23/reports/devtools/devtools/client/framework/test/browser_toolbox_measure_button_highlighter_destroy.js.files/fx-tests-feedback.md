## `Issues (first failure per run)` counts a TEST-KNOWN-FAIL as a failure mode

- Command: `fx-tests test devtools/client/framework/test/browser_toolbox_measure_button_highlighter_destroy.js`
- Expected: the Issues list to rank only real failures (TEST-UNEXPECTED-*), so each row is a distinct failure mode.
- Got: issue 2, `82x handleEvent() was unable to perform a11y checks on hidden node: id: command-button-measure ...`, looks like a separate failure mode. In the per-test profile of ORh3XuqgRQeaYJCnIQCqbw that message is a `TEST-KNOWN-FAIL`, and every one of the 20 issue-2 tasks I checked with `fx-tests task <id> --messages` also has the real failure (`Failed waitFor(): ... isButtonActive(button)`, issue 1). It is ranked "first" only because it is logged earlier in the test.
- Workaround: ran `fx-tests task <id> --messages` on each issue-2 task, then opened one profile to see the status.
- What would have answered it: tag or skip known-fail lines in Issues. Or, for each issue, show how many of its runs also contain another issue's message.
