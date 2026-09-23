## Question: the precise time of each marker in a profile longer than a minute

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on a 103 s per-test profile.
- Expected: millisecond timestamps, as in short profiles (`t=3.705s`).
- Got: `t=1m41s` / `t=1m42s` for every marker of the failing subtest, which spans 0.65 s: the order of a click, a todo, `nsAccessibilityService::Init` and the failure could not be read.
- Workaround: `--json` and a Python script printing `start/1000` to 4 decimals. Needed for every list in the investigation.
- Suggest: keep sub-second precision whatever the profile length (`t=101.976s` or `1m41.976s`).

## `--search name:task` matches `TaskController::AddTask`

- Command: `profiler-cli thread markers --search 'name:task' --list --limit 0 --session <s>`
- Expected: the harness's `task` markers (one per `add_task`).
- Got: 248 KB of output, mostly `TaskController::AddTask`, because `name:` is a substring match.
- Workaround: `--category Test`.
- Suggest: an exact-match form (`name:=task`), or say in `--help` that `field:value` is a substring match.
