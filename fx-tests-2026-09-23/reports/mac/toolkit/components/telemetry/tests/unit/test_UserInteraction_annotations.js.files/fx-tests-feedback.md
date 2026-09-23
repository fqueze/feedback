## `try --test` prints no task IDs or profile URLs even when asked

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --test toolkit/components/telemetry/tests/unit/test_UserInteraction_annotations.js --profiles --task-ids --full-messages --messages`
- Expected: the per-config table plus, for the failing config, the failure message, task IDs and the per-test profile URLs (the flags asked for them).
- Got: only the per-config ran/failed table; `--profiles`, `--task-ids` and `--messages` were silently ignored.
- Workaround: ran the whole push (`fx-tests try <rev> --harness xpcshell --profiles --task-ids --messages --limit 0`, 2,851 lines) to a file and searched it for the test. `--config` is refused on `try`, so there was no way to narrow it.
