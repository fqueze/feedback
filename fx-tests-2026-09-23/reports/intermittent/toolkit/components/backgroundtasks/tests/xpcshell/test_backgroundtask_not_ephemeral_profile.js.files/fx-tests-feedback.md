## Which worker ran each failing job?

- Command: `fx-tests test toolkit/components/backgroundtasks/tests/xpcshell/test_backgroundtask_not_ephemeral_profile.js --task-ids --limit 0`, then `fx-tests task <id> --profiles`.
- Expected: the worker (workerGroup/workerId) next to each failing task, since a failure caused by state left on a reused hardware worker shows up as the same worker failing repeatedly.
- Got: task IDs, configs and revisions, but no worker.
- Workaround: `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>/status` per task and read `runs[0].workerId`. That showed macmini-m4-104 and macmini-m4-213 each failing twice, which was the key clue.
