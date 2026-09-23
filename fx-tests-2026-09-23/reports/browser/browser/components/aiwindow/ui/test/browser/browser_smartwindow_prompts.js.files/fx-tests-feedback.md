## `fx-tests test` Issue 1 is a TEST-KNOWN-FAIL, hiding the real failure (time threshold)

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_prompts.js`
- Expected: Issue 1 to be `This test exceeded the timeout threshold ... limit was 90s`, the only `TEST-UNEXPECTED-FAIL` in the ASan runs.
- Got: `1. 645x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: main-button`. In the profiles that message is `TEST-KNOWN-FAIL` (a11y checks are off on these jobs). The timeout message is not in the Issues list at all, so the most frequent failure mode of the test is invisible, and it looks like an a11y bug. `fx-tests task <id> --messages` lists both under `FAIL` too.
- Workaround: loaded the per-test profile and filtered the test log for `TEST-UNEXPECTED-FAIL`.
- Same problem as logged for browser_smartwindow_manage_tabs_tool.js (`changed preference` known-fails). Suggestion: skip known-fail/todo results when picking the "first failure per run".

## Question: on which days did one failure mode happen?

- Question: "did the `chatContent.shadowRoot is null` mode start when the subtest that throws it was added?"
- Command: `fx-tests test <path> --task-ids --issue 4 --limit 0`, then awk over the date headers to count per day.
- `--history` gives per-day counts for the whole test only. `--history --issue <n>` (per-day counts for one Issues row) would have answered it directly: here issues 3, 4 and 8 start on 2026-09-11, the day after bug 2064128 added `test_resume_prompt_click_marks_memory_applied`.
- Correction to the entry above: the a11y message is a `TEST-KNOWN-FAIL` because AccessibilityUtils logs it with `a11yWarn`, which is `SimpleTest.todo`, on every job; not because a11y checks are off.
