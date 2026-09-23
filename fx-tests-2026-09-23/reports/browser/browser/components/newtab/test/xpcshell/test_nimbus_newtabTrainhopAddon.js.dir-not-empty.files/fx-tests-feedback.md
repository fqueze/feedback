## Question: does any failing run of this failure mode have a per-test profile, and if not, why?

- Command: `fx-tests task bR-A56QFQIa8hAHkHmsR8g --profiles`, then
  `fx-tests test browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon.js --task-ids --profiles --issue 5 --limit 0`.
- Expected: under the failing test, an explicit "no per-test profile uploaded" line, and for
  `--issue 5` a count such as "per-test profiles: 0 of 123". Ideally the reason when it is
  knowable: every message of this mode comes from a `registerCleanupFunction` callback, and
  xpcshell head.js does not upload a profile for cleanup-only failures.
- Got: `task --profiles` prints `profile <url>` under the other seven failing tests and just omits
  it under this one. `test --profiles --issue 5` prints the task IDs with no profile URL and no
  count. Absence reads like a display gap, so I listed the task's artifacts by hand to confirm,
  then read head.js to find out why.
- Workaround: `curl …/api/queue/v1/task/<id>/runs/0/artifacts` and grep; the per-test profiles
  used instead come from another failure mode of the same test, found with
  `--config windows11-32-25h2/opt` over `--json` `taskIds`.
- The same gap was logged for `test_nimbus_newtabTrainhopAddon_shutdown.js`.

## Question: which failing runs of this test on one config have a per-test profile?

- Command: `fx-tests test <path> --profiles --config windows11-32-25h2/opt,windows11-64-25h2/opt --limit 0`.
- Expected: per-test profile URLs for the failing runs on those configs.
- Got: only resource-usage profiles, with a pointer to
  `fx-tests intermittent --test <path> --profiles`, which lists only sheriff-annotated jobs (2
  Windows debug ones here), none on the configs asked for.
- Workaround: a Python filter over `fx-tests test <path> --task-ids --json` (`taskIds[]` by
  `jobName` and `message`), then `fx-tests task <id> --profiles` on a candidate.
