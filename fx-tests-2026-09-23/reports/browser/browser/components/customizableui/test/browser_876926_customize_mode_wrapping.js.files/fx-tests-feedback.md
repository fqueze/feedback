## `--issue <n>` does not select the row numbered <n> in the Issues list

- Command: `fx-tests test browser/components/customizableui/test/browser_876926_customize_mode_wrapping.js --task-ids --issue 4 --day 2026-09-21`
- Expected: the tasks of Issues row 4 as printed by `fx-tests test <path>`: `482x FAIL leaked 1 window(s) until shutdown [url = about:blank]`.
- Got: `Task IDs (issue 4: FAIL leaked window until shutdown [url = chrome://browser/content/browser.xhtml])`, which is row 5 in the printed list; `--issue 5` returned row 4's message. Rows 3 and 6 matched. Rows 4 (482x) and 5 (461x) have close counts, so the two orderings probably use different sort keys or tie-breaks.
- Workaround: read the message echoed in the `Task IDs (issue N: ...)` header and adjust N.
