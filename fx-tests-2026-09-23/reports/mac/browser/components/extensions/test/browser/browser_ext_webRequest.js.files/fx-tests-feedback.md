## For each failing run of a test: did the harness retry pass, and what failed first in that browser?

- Question: for the 31 failing runs of `browser_ext_webRequest.js`, whether the retry passed and
  whether the known leaker (`browser_ext_commands_execute_page_action.js`) failed in the same job.
- Command: `fx-tests test <path> --task-ids --limit 0` gives the task IDs only; I then ran
  `fx-tests task <id> --limit 0` 31 times and scraped the text.
- Expected: a per-task column on `--task-ids` saying "retry passed / retry failed / not retried",
  and, for `fx-tests task`, the failures in execution order (or the first failing test of the
  manifest named).
- Got: `fx-tests task` lists failing tests with those that failed their retry first, so taking the
  first entry as "the first failure" named `browser_ext_incognito_views.js` in 4 jobs where the
  leaker ran and failed earlier. One run (LSkwfsT1SyOgsi1tyVjurQ) shows "1 failing execution of
  1", i.e. no retry, with no line saying why.
- Workaround: grep each task's output for the specific tests.
