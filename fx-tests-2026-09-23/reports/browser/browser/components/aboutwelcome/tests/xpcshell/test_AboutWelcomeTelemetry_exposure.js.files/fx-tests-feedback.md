## `failures --message` says "No failure matched" for a message that is a CRASH signature

- Command: `fx-tests failures --message "child process hang at shutdown"`
- Expected: the tests behind that message (19,349 crashes in 503 tests, per `fx-tests crashes`), or a pointer to `fx-tests crashes`.
- Got: "No failure matched. ... Check --path, --message and --component for typos." — which reads as a typo, not as "crash signatures live in `crashes`".
- Workaround: `fx-tests crashes`. A hint when the message matches a crash signature would save the detour.

## Question: "which message is behind most of this job's failures?"

- Command: `fx-tests task <taskId> --profiles` lists 108 failures one by one; I needed `--json` and a script to count them by message (106 × "child process hang at shutdown").
- What would have answered it: a per-message count line at the top of the FAILED list, when most failures in a job share one message — that is what says "mass failure, this test is one of many".
