## Question: the minidump of this job's crash

- Command: `fx-tests task Xxr5rCPkSwCY8T4P8nONtQ --profiles` (and `--messages`, `--json`); also `fx-tests test <browser_compatibility_unsupported-browsers_some.js> --task-ids --limit 0`.
- Expected: the minidump ID of the `CRASH ... @ mozilla::(anonymous namespace)::RunWatchdog` it reports (the guide says `test --task-ids` gives one "where the dump was uploaded"), and the `profile_shutdown_hang_7839.json` artifact.
- Got: the CRASH status and signature, but no minidump ID and no shutdown-hang profile, although the task has `public/test_info/7b1314b8-f89a-42eb-d98c-6775f5d5372f.dmp` and `profile_shutdown_hang_7839.json`.
- Workaround: listed the task's artifacts with `curl .../runs/0/artifacts`, then `fx-tests crash Xxr5rCPkSwCY8T4P8nONtQ 7b1314b8-... --harness mochitest --all-threads`.
- What would have answered it: print the minidump ID(s) and the shutdown-hang profile URL next to the CRASH row in `fx-tests task`.

