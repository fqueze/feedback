## Question: in how many runs did this failure mode occur at all, not only as the first failure?

- Command: `fx-tests test gfx/layers/apz/test/mochitest/test_group_keyboard.html --task-ids --limit 0`
- Expected: a count of runs per failure mode, including runs where the mode was a later message (here, a backward jump in the `scrollTo` subtest after a stall in the `scrollBy` subtest).
- Got: "Issues (first failure per run)": the backward-jump mode shows up in 3 rows, but it is in 7 of the 15 failing runs. The other 4 were only visible through `fx-tests task <id> --messages`, one job at a time.
- Workaround: the per-job `fx-tests task` dumps an earlier report had saved.
- What would have answered it: `fx-tests test <path> --issue <n> --any-position` (or a column in Issues) counting the runs that contain a message matching the mode anywhere, with their task ids.
