## The profile of the hung browser behind an "application timed out ... with no output" failure

- Command: `fx-tests task Ro-Jj1IpTG6_cPtNHsWumA.0 --profiles` (also FXhGPzdhTSuFbnyrrdvadw.0, VEMg6HbKSlGXigmEAc22lQ.0)
- Expected: the job's `public/test_info/profile_0_<pid>.json` listed as the failure's profile. It is a full Gecko profile of the hung browser (parent pid, all processes), up to the hang, and it was the most useful artifact.
- Got: "No failing test named a per-test profile in this job." The file is only visible by listing the task's artifacts through the Taskcluster API.
- Workaround: `curl .../task/<id>/runs/0/artifacts` and look for `profile_0_*.json`.

## The minidumps of a hang

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --json`
- Expected: the minidump IDs of the CRASH/TIMEOUT entry, so `fx-tests crash <task> <dump>` can be run directly (the guide says `--task-ids` gives one where the dump was uploaded).
- Got: no minidump ID anywhere, although the tasks carry `<uuid>.dmp` artifacts and the resource-usage profile's CRASH marker names one.
- Workaround: list Taskcluster artifacts for `*.dmp`.

## Which of a Windows hang's dumps is the parent process

- Command: `fx-tests crash PU7IBddOQj-MYPJ1rrlKfQ.0 <dump>` for each of 3 (and 10, and 9) dumps
- Expected: each dump labelled with its process type (parent / tab / gpu ...), or `task` listing them that way.
- Got: "the dump ... records no crashing thread" for every one; had to run `--all-threads --frames 0` on each and grep for the parent's stack. One parent dump (bVevkfNBSoakVHaAh3lxLw 36544a17-...) is a 61-byte artifact and reported as "the artifact is not there", which is fine but costs a full scan to learn.
