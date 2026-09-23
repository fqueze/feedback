## Question: does any failing run of this test have a per-test profile?

- Command: `fx-tests task <taskId> --profiles` on 5 failing jobs, then
  `fx-tests test <path> --profiles`.
- Expected: for a failing test with no per-test profile, an explicit "no per-test profile
  uploaded" line (ideally with the likely reason: this test's failures are all reported from
  `registerCleanupFunction` callbacks, which do not set `_passed = false` in xpcshell head.js,
  so no failure profile is uploaded).
- Got: `task --profiles` prints `profile <url>` under other failing tests and just omits the
  line under this one, so absence reads like a display gap. `test --profiles` lists only
  resource-usage profiles and points at `fx-tests intermittent --test <path> --profiles`.
- Workaround: a shell loop over the Taskcluster artifact listings of all 219 failing tasks
  (`/api/queue/v1/task/<id>/runs/<n>/artifacts`), grepping for the test name: 0 of 219 had one.
  About 220 HTTP calls and a few minutes.
- What would have answered it: a count in `fx-tests test <path> --profiles`, e.g.
  "per-test profiles: 0 of 219 failing runs".

## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/newtab/test/xpcshell/test_nimbus_newtabTrainhopAddon_shutdown.js --bugs`
- Expected: a "Bugs: none found" line.
- Got: the normal output with no bug section at all, identical to running without `--bugs`;
  had to check `--json` (`annotatedBugs: []`) and Bugzilla to be sure.
