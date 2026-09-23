## Question: "do these two tests fail in the same jobs"

- Commands: `fx-tests test <switcher> --since 8 --task-ids --limit 0 --json` and the same for `browser_aiwindow_group_tabs_button_model.js`, then a Python set intersection on `taskIds[].taskId`.
- Answer: 133/133 — every job failing the switcher also failed the model test, and every linux-swr job failing the model test also failed the switcher. That was the key evidence for "victim of", and nothing in the default output could show it.
- What would have answered it: `fx-tests test <path> --co-failures` (tests failing in the same jobs, ranked by overlap), or `fx-tests task` style "also failed in N of these jobs" in `test --task-ids`.

## Question: "which failure message goes with which run of the job, and on which config / period"

- Command: `fx-tests test <path> --task-ids --limit 0 --json`, then grouping `taskIds[]` by taskId (message combination per job) and by (message, platform, before/after a date).
- Answer: every failing job had exactly one browser.xhtml-leak run and one about:newtab/docShell-leak run; the aiWindow.html and most a11y messages were Windows/macOS only and stopped on 2026-09-05.
- What would have answered it: an Issues block that can be split by config (`--config` works, but gives no per-period view) and a "message combinations per job" view.

## `task --messages` lists known-fail "changed preference" under FAIL

- Command: `fx-tests task SYfN0cj0TA-TIXEurNwgFQ --messages`.
- Got: `2x changed preference: browser.smartwindow.lastSmartWindowUsageTime` listed first among the failure messages of a FAIL test.
- These are `TEST-FAIL` todo results (browser-test.js `checkPreferencesAfterTest`: `pass: !gConfig.comparePrefs, todo: !gConfig.comparePrefs`), not unexpected failures; they appear on every run including passing ones. Listing them with the real failures sends one looking at the wrong message. Mark them as known-fail or drop them.
