## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test toolkit/components/resistfingerprinting/tests/browser/browser_usercharacteristics_timezone.js --bugs`
- Expected: a line such as "No sheriff-annotated bugs name this test".
- Got: the same output as without `--bugs`, with no bugs section at all, so I could not tell "no bug" from "the flag did nothing / the query failed".
- Workaround: `--json` and read `annotatedBugs` (`[]`).

## Question: "a job of the same config, on the same push, that ran this test and passed"

- Needed: a passing run of the same manifest to compare its resource-usage profile with the failing one (the brief says no per-test profile exists for a pass, so the job timeline is the only comparison).
- Commands tried: `fx-tests test <path> --task-ids` (failures only), `fx-tests task <id> --passed` (works, but only once you already know which job ran the test).
- Workaround: listed the push's Taskcluster task group with a script (`/api/queue/v1/task-group/<id>/list`, paginated), then looped `fx-tests task <id> --passed --limit 0 | rg <test>` over candidate chunks until one contained the test. About ten calls.
- What would have answered it: `fx-tests test <path> --task-ids --passed` (or `--coverage --task-ids`) listing some passing task IDs per config, ideally on the same revisions as the failures.


## Question (review, again): "which passing jobs on this push ran this test, per config"

- Needed: passing controls on other configs (Linux opt, Windows debug) from the failing push, to check their resource-usage profiles for a log line.
- Commands: `fx-tests task <id> --passed --limit 0 | rg -c <test>` looped over chunks listed from the task group: 11 calls for the first pass, then 6 more over the linux2404-64/opt no-nv chunks until chunk 13 matched.
- What would have answered it: `fx-tests test <path> --passed --task-ids --rev <rev>` (or `fx-tests try <rev> --test <path>`), listing the jobs that ran the test on that push, with their status.
