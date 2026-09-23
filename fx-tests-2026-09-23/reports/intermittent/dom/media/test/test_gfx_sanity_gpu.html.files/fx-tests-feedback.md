## Question: which machine ran each failing job, and each passing one, of this config?

- Command: `fx-tests test dom/media/test/test_gfx_sanity_gpu.html --task-ids --limit 0`
- Expected: the worker (e.g. `t-nuc12-004`) next to each failing task, and some way to list passing jobs of the same config with their workers, to tell a machine-specific failure from a revision-specific one.
- Got: task IDs only; no passing jobs at all.
- Workaround: `curl .../queue/v1/task/<id>/status` per task for `workerId`, plus the Treeherder jobs API (`job_type_name=...&last_modified__gt=...`) for passing jobs and `machine_name`, joined in a Python script. About 10 minutes.
- What the output could have shown: a `machine` column in `--task-ids`, and a per-machine pass/fail table (like the per-config one) for hardware pools.

## Question: which GPU driver was each failing job's machine running?

- Command: none available; `fx-tests task <id> --profiles` does not show it.
- Expected: the adapter and driver version, which the per-test profile already records (the `sanity-test.driver-version` pref reads, the gfx info in the profile).
- Got: nothing.
- Workaround: downloaded 13 failure profiles and grepped them for `3x.0.101.xxxx`. That grep found the answer: every `FailedVideoEncode` ran on Intel 32.0.101.7085 and the older failures on 31.0.101.3729.
- What the output could have shown: GPU vendor, device and driver in `fx-tests task`'s header, taken from the profile's metadata.
