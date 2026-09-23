## `fx-tests try` task list is duplicated and truncated even with `--limit 0`

- Command: `COLUMNS=250 fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- Expected: the 3 distinct failing tasks of `toolkit/content/tests/chrome/test_panel.xhtml`, each once.
- Got: 5 task lines (each task printed twice, once per failing execution) and `… 1 more task`, despite `--limit 0`. The hidden one was only the duplicate of `Vk8RoYlOT5CDUEz527I46A`, which took `--json` to find out.
- Workaround: `--json` and read `permaFails[].taskIds`.

## `fx-tests try --profiles` lists only the first per-test profile, not the retry's `-2`

- Command: same as above.
- Expected: both `profile_test_panel.xhtml.json` and `profile_test_panel-2.xhtml.json` per task, as `fx-tests task <id> --profiles` lists them (the outcome line even says "failed, then failed again on rerun").
- Got: only `profile_test_panel.xhtml.json`; JSON `profiles[].testProfiles` also has one entry.
- Workaround: `fx-tests task <taskId> --profiles` per task.
