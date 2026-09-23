## Question: which tests are behind this crash signature?

- Command: `fx-tests crashes --harness xpcshell --signature ShutdownThreadPoolInternal --limit 0`
- Expected: the tests that hit the signature, with counts (the row says `tests 4`).
- Got: only `111  4  0  @ mozilla::TaskController::ShutdownThreadPoolInternal`; the four tests are not named.
- Workaround: `--json`, where `rows[].tests[]` names them (triggeringPrincipal 68, trustedtypes 37, dnr_domainType 5, test_staticPartition_prefetch 1). The text output could list them under the row.

## Question: the profile URLs of one failure mode's jobs

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_triggeringPrincipal.js --profiles --task-ids --issue 8 --limit 0`
- Expected: the profiles of the 67 jobs behind issue 8 (CRASH @ TaskController::ShutdownThreadPoolInternal).
- Got: the `Profiles` block lists resource-usage URLs for all 459 failing jobs (Linux, Android, opt-msix...): `--issue` filters the task-ID block but not the profile block. Without `--task-ids`, `--profiles --issue 8` is refused.
- Workaround: none needed here, since the resource-usage URL comes from the task ID. The profile block could honour `--issue`, and could say "no per-test profile (crash)" when a failure has none.

## Question: which process crashed, and when

- Command: `fx-tests crash TEb0NPw7TOibhUQQG9MpHw.0 1719aeeb-57d3-4dd1-acc0-6c22ce51ef20`
- Expected: the process type, and when the process started, next to the signature.
- Got: the stack only. The answers were in the dump's `.extra` (`RemoteType = web`, `URL = http://example.com/page.html`, `IPCShutdownState`) and in `--raw` (`pid`, `process_uptime`).
- Workaround: `curl` the `public/test_info/<dump>.extra` artifact, and `--raw`. The default output could print `RemoteType`, `URL`, `MozCrashReason`, `pid` and `process_uptime`.
