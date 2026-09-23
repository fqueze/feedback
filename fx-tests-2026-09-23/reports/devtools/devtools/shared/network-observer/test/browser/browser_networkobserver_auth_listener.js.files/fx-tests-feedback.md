## Question: did the manifest-level leak that a test's `skip-if` exists for happen on this try push?

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/shared/network-observer/test/browser/browser_networkobserver_auth_listener.js`
- Expected: next to the per-config pass counts, any harness-level failures attributed to the test's manifest in those jobs (`TEST-UNEXPECTED-FAIL | LeakSanitizer ... | .../browser.toml`, `leakcheck ... | .../browser.toml`). Leaks are the most common reason for a `skip-if` ("Disabled for frequent leaks"), and they are reported per manifest, never per test.
- Got: 21/21 passed, and nothing about manifest leaks. `fx-tests failures --harness mochitest --message LeakSanitizer` and `fx-tests errors --message LeakSanitizer` also match nothing, so these failures seem absent from the data entirely.
- Workaround: loaded each job's resource-usage profile (9 of them) and searched for `LeakSanitizer`, `Leaked Object`, `Leaked Total` markers, plus a pre-fix job as positive control.

## Question: the task IDs of every job that ran one manifest on a try push

- Command: `fx-tests try <rev> --task-ids --limit 0 --full-messages`
- Expected: `--limit 0` lifts every truncation.
- Got: each row still ends in `… 37 more tasks`; `--config` is refused on `try`.
- Workaround: `--json` and a script over `permaFails[].taskIds`. The `--test` table could print the task IDs per config with `--task-ids`; it currently ignores the flag.
