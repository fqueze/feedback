# fx-tests feedback (1809667.remote-devices)

## `fx-tests task` on a Firebase Test Lab job blames artifact expiry

- Command: `fx-tests task JtJhfGuOQ2C6JOE16N9MIg --profiles` (ui-test-apk-fenix-arm-debug, run on 2026-09-21, one day old)
- Got: `task JtJhfGuOQ2C6JOE16N9MIg.0 has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent ... A job that is not a test job never uploads one.`
- Expected: to be told this job type (flank, not mozharness) never uploads a resource profile, since the task is one day old; the expiry explanation is wrong here.
- What the output could have shown instead: the artifacts the task did upload. For an FTL job, `public/results/matrix_ids.json` holds the matrix id, its state and billable minutes, which was the only runtime evidence in this investigation.

## Question: for a meta bug, which remote-device jobs hit the limit, annotated or not, and on which device?

- Command: `fx-tests intermittent --bug 1809667` then the Treeherder jobs API by platform (`th/`, `thplat.py`).
- The device (`machine_name`, e.g. `pixel6-138`) was the discriminator for the Bitbar mode: 69 Fenix speedometer3 runs at the limit, all on that one device, 4 of them annotated here. `fx-tests` shows neither the machine nor unannotated runs at the limit.
- What the output could have shown: per annotated occurrence, its machine, and per machine the share of its runs that hit maxRunTime.
