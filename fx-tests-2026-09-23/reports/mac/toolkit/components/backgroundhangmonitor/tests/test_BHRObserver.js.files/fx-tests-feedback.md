## Question: "which execution got which message?" (one job, first run and harness retry)

- Command: `fx-tests task WgINS7cnSseqypcayKXhjA --messages`
- Expected: per execution (first run / retry), its messages.
- Got: `TIMEOUT — 2 failing executions of 2` / `1x [test_BHRObserver : 157] ... false == true` /
  `Test timed out` (no count), so it is ambiguous whether the retry also hit line 157.
- Workaround: loaded the resource-usage profile and read the `test` markers and the log markers per run.
- What the output could have shown: the messages grouped under each execution, labelled first run / retry.

## Listed per-test profiles that cannot be loaded

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids` and `fx-tests task <id> --profiles`
- Got: 4 test profiles for test_BHRObserver.js, of which 3 are corrupted (child and parent process wrote the
  same timeout-dump path; see profiler-cli-feedback.md). Cost three failed loads (one of them 761 MB).
- Suggestion: the artifact size is already known (the resource-usage profile has `artifact` markers:
  `profile_test_BHRObserver_retry.js.json — 761MB`); printing it next to each profile URL would at least let
  the reader pick the small ones first.
