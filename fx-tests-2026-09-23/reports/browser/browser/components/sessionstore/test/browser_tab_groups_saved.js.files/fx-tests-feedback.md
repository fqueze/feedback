## Question: which failing tasks of this test have a per-test profile?

- Commands: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --profiles` on 5 TSan tasks before finding that none had one ("No failing test named a per-test profile in this job"). ASan and debug-standalone tasks do have one.
- 81 of the 89 failures were on TSan, so picking tasks from the worst config, as the brief says, wasted about 6 calls.
- What would have answered it: a marker in the `--task-ids` list for the tasks whose failure message names a profile (or a `--profiles` flag on `fx-tests test`, as `fx-tests try` has), or a note in the per-config table that a config never uploads per-test profiles.
