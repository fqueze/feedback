## `try --test` ignores `--task-ids` and `--profiles`

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --test devtools/client/webconsole/test/browser/browser_console_clear_method.js --task-ids --profiles`
- Expected: the per-config table, plus the task IDs and profile URLs behind this test's failures.
- Got: only the per-config table (jobs/failed/passed on retry/passed); no task IDs, no profile URLs, and no failure message.
- Workaround: ran the whole-push report (`fx-tests try <rev> --task-ids --profiles --messages --limit 0`, 2851 lines) into a file and searched it for the test. The question was "the failing tasks and per-test profiles of one test on a try push".

## `try --help` lists `--config`, which `try` then refuses

- Command: `fx-tests try <rev> --task-ids --config devtools-chrome --limit 0`
- Expected: rows narrowed to devtools-chrome jobs, as `--help` suggests.
- Got: exit with "--config cannot be applied to try". The refusal is well explained, but the flag appears in `try --help`.
- Workaround: none needed.
