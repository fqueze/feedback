## Question: "the minidump of the crash in this job" when no minidump ID is listed

- Command: `fx-tests task Xxr5rCPkSwCY8T4P8nONtQ --profiles` / `--messages` / `--json`
- Expected: the minidump ID of the `CRASH ... @ mozilla::(anonymous namespace)::RunWatchdog` of `browser_compatibility_unsupported-browsers_some.js`, to feed `fx-tests crash`.
- Got: the signature only; `--json` has `statusCounts.CRASH 1` and the `@ RunWatchdog` message, no minidump ID. `fx-tests test <that test> --task-ids` did not list it either.
- Workaround: downloaded `public/logs/live_backing.log` and grepped `PROCESS-CRASH` (ID `7b1314b8-f89a-42eb-d98c-6775f5d5372f`) and the symbolicated thread stacks from the log.
