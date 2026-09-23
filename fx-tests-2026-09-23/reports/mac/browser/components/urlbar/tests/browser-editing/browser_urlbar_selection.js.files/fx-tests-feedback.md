## `--limit 0` does not expand a test's task, config and profile lists in `fx-tests try`

- Question: the task IDs, configs and profile URLs of every failing job of one test on a try push.
- Command: `COLUMNS=250 fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0 --full-messages`
- Expected: all 8 configs, all 16 tasks and all 32 profile URLs of `browser_urlbar_selection.js`.
- Got: `… 4 more configs`, `… 27 more tasks`, `… 11 more profiles` under the test, despite `--limit 0`.
- Workaround: `--json` and a script over `permaFails[].taskIds` / `.profiles` / `.jobNames`. The JSON's
  `profiles[].testProfiles` also held only the first profile of each task, while `fx-tests task <id> --profiles`
  showed the retry's `-2` profile as well.
- Also: the task list prints each task twice with the same `.0` suffix (`task bDJeoZJNTKib3ns2mD4WsA.0` twice),
  once per execution (first run and harness rerun); saying which line is the rerun would avoid the
  "is this a duplicate?" check.
