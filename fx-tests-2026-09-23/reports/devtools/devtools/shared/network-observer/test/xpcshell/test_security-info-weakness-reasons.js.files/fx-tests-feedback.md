## `fx-tests task <id>` without the run suffix reports the failing run's artifact as expired

- Command: `fx-tests task JieLxwg4RZqcUMMGIY6SQQ --profiles`. The ID came from `fx-tests test <path> --task-ids`, which printed `JieLxwg4RZqcUMMGIY6SQQ.1`; I dropped the suffix.
- Expected: the run that failed (.1), or a note that .0 is not the failing run.
- Got: `task JieLxwg4RZqcUMMGIY6SQQ.0 has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent`. The task was 6 days old. Run .0 was the one that never uploaded, not an expired one.
- Workaround: `fx-tests task JieLxwg4RZqcUMMGIY6SQQ.1 --profiles`.

## Question: "why did this Android xpcshell run fail?" (Issues says only "Failure details not recorded")

- Command: `fx-tests test devtools/shared/network-observer/test/xpcshell/test_security-info-weakness-reasons.js`
- Got: `2x FAIL Failure details not recorded (likely Android or platform logging issue)`.
- The answer was in each job's resource-usage profile, as an INFO marker naming the test: `remotexpcshelltests.py | Failed to start process: <test> | 0 | Could not kill left-over process`. Showing that line when no failure message was recorded would make the diagnosis, and grouping by it would show the 3-4 other tests per job failing the same way.
