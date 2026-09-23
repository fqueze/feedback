## `fx-tests test <path> --bugs` prints no bug section when there is none

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marSuccessPartial.js --bugs`
- Expected: a line such as "Bugs: none name this test".
- Got: the same output as without `--bugs`, with no bug section. That reads the same as the flag being ignored.
- Workaround: took it to mean no bug.

## Question: "which failing runs have a per-test profile small enough to load?"

- Command: `fx-tests task <taskId> --profiles`
- Expected: the size of each profile artifact next to its URL.
- Got: URLs only. All of this test's opt-build profiles are 50-57 MB, and profiler-cli cannot load them (see profiler-cli-feedback.md).
- Workaround: `curl -sIL` on all 49 artifacts, reading `x-goog-stored-content-length` (profile-sizes.txt).

## Question: "in how many of this test's failing jobs did a sibling test fail too?"

- Command: `fx-tests task <id>` once for each of the 49 task IDs, grepping the FAILED list for `unit_update_binary` (cofail.txt).
- Expected: `fx-tests test <path> --task-ids` listing, for each job, the other tests that failed in it, or a co-failure count per test.
- Got: task IDs only, so it took 49 more calls.
