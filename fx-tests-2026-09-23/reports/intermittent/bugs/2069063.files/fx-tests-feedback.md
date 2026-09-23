## `fx-tests task` says a gtest job's resource profile "records no tests at all", but it does

- Command: `fx-tests task NbnNaDVyS7WauxPsy4X5JA --profiles` (test-linux2404-64/debug-gtest-1proc)
- Expected: the job's gtest outcomes, or a message saying gtest is not supported.
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one — the log on Treeherder is the next step." Plus a warning that the FAIL marker "named no test path".
- The same `profile_resource-usage.json`, loaded in profiler-cli, has 15709 `test` markers with Status (e.g. `FAIL — TestAudioTrackGraph.TailDispatchFromMicroTaskDuringShutdown`, 49 ms) and 10 `FAIL` markers with the gtest message. gtest names are `Suite.Test`, not paths, which seems to be why they are dropped.
- Workaround: loaded the resource profile in profiler-cli and searched `test` markers by name.
- The message sends the reader toward "the harness died", which is wrong here. Either list gtest `Suite.Test` names, or say that gtest jobs are not parsed.
