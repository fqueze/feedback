## `fx-tests task <id> --profiles` refuses tasks killed for max-run-time, although their per-test profiles exist

- Command: `fx-tests task BMYU5cgaSUyNKx7-zX99Zg --profiles` (also Mbf9Tcm9QV61KIegvnjLrQ, HhihQL8jQ9um0WeqDR6RmA, QErlhLpxRrmTViQvNENlZg, fw9r-cjFRGGoNuO5WZDkDw, Q70T9OA9STO8LMswqHO2VQ).
- Expected: the per-test failure profiles and the resource-usage profile.
- Got: "task ... was killed for exceeding its maximum duration, so its profile is a partial stream ... The job never got to write a profile, so there are no per-test results to read." Yet the task's artifact list has `profile_resource-usage.json` and ten `profile_test_*.html.json` per-test profiles (including `profile_test_device_sensor_event.html.json` and `-2`).
- Workaround: list artifacts with `curl .../api/queue/v1/task/<id>/runs/0/artifacts` and build the URLs by hand. The question it could not answer: "the profile URLs of this failure's occurrences", for every one of the 32 jobs annotated on bug 1792846.

## `fx-tests test <path>` does not see the failing config at all

- Command: `fx-tests test browser/components/resistfingerprinting/test/mochitest/test_device_sensor_event.html` (and `--coverage`, `--task-ids`).
- Expected: the 32 annotated failures on `test-macosx1500-aarch64-vms/opt-mochitest-plain` (bug 1792846, 2026-09-16 to 2026-09-22).
- Got: 1 failure in 21 days, on linux wayland; no `-vms` config listed in coverage. Presumably because these jobs hit max-run-time and wrote no finished resource-usage profile, so they are absent from the index.
- Workaround: `fx-tests intermittent --bug 1792846 --limit 0`. The verdict "intermittent, 0.5% on one linux config" is misleading for a test that fails every run of a new config.

## Correction to the two entries above

- `fx-tests intermittent --bug 1792846 --profiles` (and `--json`, field `profiles`) does list the per-test profile URLs of these max-run-time jobs, so the URLs were reachable; what cost time is that `fx-tests task` says flatly "there are no per-test results to read", which sent me to the raw artifact list. Pointing at `intermittent --bug --profiles` there would have saved it.
- "fails every run of a new config" above is wrong: from `treeherder-cli --similar-history`, 22 of 94 recent `test-macosx1500-aarch64-vms/opt-mochitest-plain-3` jobs hit max-run-time this way. The point stands that `fx-tests test` shows none of them.

