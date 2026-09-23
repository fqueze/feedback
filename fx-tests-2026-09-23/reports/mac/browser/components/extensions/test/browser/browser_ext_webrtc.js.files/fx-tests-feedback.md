## Question: in which jobs did the leaker fail but this victim pass, and what cut the cascade there?

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webrtc.js --task-ids --limit 0`, then `fx-tests task <taskId>` for jobs where `browser_ext_commands_execute_page_action.js` failed.
- Expected: a way to see one test's outcome (pass / fail / not run / not rerun) across the jobs where another test failed, plus what ended the browser session in between (a crash, a timeout followed by a restart).
- Got: `test --task-ids` lists only the jobs where this test failed; `task` text output lists only failures, so "this test passed here" is invisible without `--json`.
- Workaround: looped `fx-tests task <id> --json` over 44 jobs and read `passed[]` and `failures[].statuses` in a script (`jobs.txt`).
- What the output could have shown: `fx-tests test <victim> --with-failing <leaker>` (victim's status in every job where the leaker failed), or `fx-tests task <id> --test <path>` printing one test's executions even when they passed.

## `passedOnRerun: false` also means "never rerun"

- Command: `fx-tests task LSkwfsT1SyOgsi1tyVjurQ --json`
- Expected: a field that tells "failed again on the rerun" from "not rerun".
- Got: `browser_ext_webrtc.js` has `passedOnRerun: false` with `executionCount: 1`; the harness retry stopped at `browser_ext_incognito_views.js`'s timeout and never reached it. I first read it as "the retry failed too".
- Workaround: checked `executionCount` / the text output's "1 failing execution of 1".
