## `fx-tests test` says the test stopped failing, while sheriffs starred 16 jobs on it in the same days

- Command: `fx-tests test dom/base/test/test_bug331959.html --history` (window 2026-08-31 … 2026-09-20).
- Expected: the failures of 2026-09-16 … 2026-09-20 counted, or at least a line saying some jobs could not be read.
- Got: 0 failures every day since 2026-09-11, `recent 0.0%` on every config. `fx-tests intermittent --bug 2031724` lists 16 annotated jobs from 2026-09-16 to 2026-09-20 (all `macosx1500-aarch64-vms`). Every one of them was killed at `maxRunTime` (3600 s), so the per-test data never got into the aggregate. Its `Verdict` and `--history` read as "no longer fails", which is wrong: this is now the worse failure mode, because the retry fails too and takes the whole job down.
- Workaround: `fx-tests intermittent --bug <N>`, then Taskcluster artifact listings by hand.

## `fx-tests task` gives up on killed jobs, although their per-test profiles are there

- Command: `fx-tests task FW58XVpxQTS1nW4RZ6d1fg --profiles` (and 7 other annotated tasks of bug 2031724).
- Expected: the `profile_test_*.json` artifacts listed, which is what `--profiles` is for.
- Got: `was killed for exceeding its maximum duration, so its profile is a partial stream ... there are no per-test results to read`. But the task's artifact listing has `profile_test_bug331959.html.json`, `profile_test_bug331959-2.html.json` and 12 more per-test profiles, and `profile_resource-usage.json` is a JSONL stream with every `test` marker, readable with `jq`.
- Workaround: `curl .../runs/0/artifacts | jq '.artifacts[].name'`, and `jq` over the JSONL resource-usage stream to get the job timeline.

## Question: which manifest ran just before the failing test's manifest, in each failing job?

- Needed to tell whether a neighbouring manifest (here `dom/base/test/fullscreen/mochitest.toml`) is common to every failure. Scripted over the Taskcluster task payload's `MOZHARNESS_TEST_PATHS` for all 40 task IDs from `fx-tests test --task-ids`, and again for recent passing tasks found through the Treeherder API.
- What could have shown it: `fx-tests test <path> --task-ids` printing the preceding manifest (or the chunk's manifest list) next to each task, and the same for a sample of passing runs.

## Issues block says "Failure details not recorded" on macOS

- Command: `fx-tests test dom/base/test/test_bug331959.html`.
- Got: `40x FAIL Failure details not recorded (likely Android or platform logging issue)`, while all 40 failures are on macOS and `fx-tests task <id>` prints the messages (`[SimpleTest.finish()] No checks actually run.`). The failure mode could not be told apart from the test page without opening a task.

