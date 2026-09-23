## The failure messages, task IDs and profiles of one test on a try push

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --harness xpcshell --test toolkit/modules/tests/xpcshell/test_DeferredTask.js --profiles --task-ids --messages --full-messages`
- Expected: that test's section: failure messages with counts, task IDs, profile URLs.
- Got: only the per-config table (jobs / failed / "passed on retry ≥0" / "passed ≤0"). `--profiles`, `--task-ids` and `--messages` were silently ignored.
- Workaround: ran the same command without `--test`, with `--limit 0`, and grepped the 1000-line output for the path.
- Also: the section lists "task X" rows but truncates them at 5 ("… 3 more tasks") even with `--limit 0`, while the profile URL list below them is complete.
