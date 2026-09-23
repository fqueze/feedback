## Question: which job did this confirm-failure (`-cf`) job confirm?

- Commands: `fx-tests test browser/components/aboutlogins/tests/chrome/test_login_item.html --task-ids --limit 0`, then `fx-tests task aXKirSbrT9GaPR8WK3sF2Q --profiles`
- Expected: the `-cf` job's task line naming the original failing job (d6XM1k2_Sjquqb1U6OjEpQ), since the only failing job listed was a `-cf` one and the regular config reads 314/314.
- Got: no link to the original. I had to curl the task definition twice: the `-cf` task's `extra.parent` is the action task (IWnUSVugQ1ijhHDIhrKf3g), and only that task's `ACTION_TASK_ID` env var names the original job. `fx-tests task IWnUSVugQ1ijhHDIhrKf3g` fails with "no profile_resource-usage.json", which reads as an expired artifact, not "this is an action task".
- What the output could have shown: for a `-cf` job, a `Confirms: <taskId> <job name>` line. For an action task, say that it is one.
- Workaround: `curl .../queue/v1/task/<cf task>` -> `extra.parent`, then `curl .../queue/v1/task/<parent>` -> `payload.env.ACTION_TASK_ID`.
