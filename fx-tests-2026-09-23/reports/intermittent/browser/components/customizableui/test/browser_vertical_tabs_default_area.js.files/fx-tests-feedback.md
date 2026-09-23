## `fx-tests test` verdict "passing" for a test that fails every test-verify run

- Command: `fx-tests test browser/components/customizableui/test/browser_vertical_tabs_default_area.js` (also `--history`, `--coverage`)
- Expected: the 9 failing test-verify jobs of 2026-09-03 (all annotated on bug 2068983) to appear, or a note that TV jobs are excluded.
- Got: `9,591 runs 9,591 pass (100.00%)`, `Verdict: passing.`, and no verify config under `--coverage`. Only `fx-tests intermittent --bug 2068983 --since 60 --tree all` revealed that the failures exist, all of them `test-verify`.
- Workaround: `intermittent --bug` with a widened window. A test that only fails under TV (repeat in the same browser) reads as perfectly healthy. A line like "test-verify jobs not counted" would prevent the wrong conclusion.

## `fx-tests task` says "Passed when the harness reran it." when the retry also failed

- Command: `fx-tests task MhxNh6ONQXCliVa-Sz9Qtg --profiles` (same for H1TA12HMRSKa0yDqsBuS3A)
- Expected: in a test-verify job, the retry browser runs the test twice, and its second execution failed (the `-2` profile exists, and the resource-usage profile shows `retry` → PASS then FAIL). The output should say the retry failed too, or at least "passed once, then failed again".
- Got: `FAIL — 2 failing executions of 4` followed by `Passed when the harness reran it.`, which contradicts the brief's rule "two profiles mean the retry failed too".
- Workaround: read the resource-usage profile's `test` markers.
