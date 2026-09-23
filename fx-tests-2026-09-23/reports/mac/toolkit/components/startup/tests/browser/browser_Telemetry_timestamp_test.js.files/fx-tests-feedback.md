## `task --profiles` does not list the hang profile of an "application timed out" failure

- Command: `fx-tests task VafvD-fkQpeiToJpgfzYAQ.0 --profiles`
- Expected: the profile the harness captured of the hung browser (it sends SIGUSR1/SIGUSR2 after
  "application timed out after 370.0 seconds with no output"), which is uploaded as
  `public/test_info/profile_0_4424.json` (63.8MB), listed under the failing test.
- Got: "No failing test named a per-test profile in this job." The only way to find it was the
  `artifact` marker `profile_0_4424.json` in the resource-usage profile, then listing the task's
  artifacts by hand with curl.
- Workaround: `curl .../runs/0/artifacts` and pick `profile_0_<pid>.json`.
- This is the only profile of the hang itself for this failure mode, so it is the one that matters.

## Question: which minidump of a Windows hang is the parent process?

- Command: `fx-tests crash CizjR0jPR6qeOIL49LJ7kA.0 <id> --thread 0` over every `.dmp` in the task's
  artifacts (found with curl: `fx-tests task --profiles` and `test --task-ids` list no minidump IDs
  for this "application timed out" failure, although the harness wrote one per process).
- Expected: the dump IDs of the hang, each with its process type (the harness log names the pid,
  and the pid log gives `gpu`/`tab`/`rdd`/`utility` for the children), parent first.
- Got: no IDs anywhere; then one call per dump until one showed a JS stack on its main thread.
- Also: for `7025a4dd-...` (the harness logged "unable to dump minidump file for pid 7224"), `crash`
  said "the artifact is not there ... Taskcluster expires artifacts, so a dump from an old task is
  permanently gone", although the task is 12 days old and its other dumps load: the message
  blames expiry for a dump that was never written.
