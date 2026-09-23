## The task IDs and profile URLs of one test's failures on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --test devtools/client/application/test/browser/browser_application_panel_open-links.js --profiles --task-ids --messages`
- Expected: the per-config table, plus the failing task IDs and the test's per-test profile URLs, since `--task-ids` and `--profiles` were passed.
- Got: only the per-config jobs/failed/passed table. `--task-ids` and `--profiles` were silently ignored with `--test`.
- Workaround: run the whole push (`fx-tests try <rev> --task-ids --profiles --messages --full-messages --limit 0`, 2,851 lines) into a file, then grep for the test name to find its two tasks and profile URLs. `--config devtools-chrome` to narrow it is refused for `try`.
- What the output could have shown: under `--test`, the failing task IDs per configuration and the `profile_<test>.json` / `-2` URLs, the same lines the full push view prints for that test.
