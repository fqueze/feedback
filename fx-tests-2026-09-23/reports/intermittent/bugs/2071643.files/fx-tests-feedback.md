# fx-tests feedback (bug 2071643 report)

## `intermittent --bug`: when did it start and stop, per tree and per push?

- Command: `fx-tests intermittent --bug 2071643 --tree all --since 30`
- Expected: a per-day or per-push breakdown showing that the bug hit 5 central nightlies (09-12 to 09-14) and then one beta push (09-21), and that nothing happened since.
- Got: the default output lists jobs one row per locale, 806 names at "2x" each (`test-update-integrity-en-GB-linux64-shippable`, ...), and a flat, truncated list of occurrences. The per-day `history` array and the tree/revision of each occurrence are only in `--json`.
- Workaround: a Python script over `--json` grouping `occurrenceRows` by (tree, revision, pushTime) and by job kind (all `test-update-integrity-*` merged). That gave 2312 central integrity jobs, 815 beta integrity jobs, 23 beta update-verify chunks and 3 mis-stars.
- What could show it: a per-day history line, like `fx-tests test --history`, and grouping job names with the locale stripped as well as the chunk number.

## `intermittent --bug` default window hides the only recent occurrences

- Command: `fx-tests intermittent --bug 2071643`
- Got: `no sheriff annotations for bug 2071643 on trunk between 2026-09-16 and 2026-09-22`. With `--tree all`, the same window has 839 annotations, all on mozilla-beta.
- The hint does suggest `--tree all`. It could say instead that N annotations exist on other trees in that window, since the tool already fetches them with `--tree all`.

## `task --profiles` on a non-test job reads as an expired artifact

- Command: `fx-tests task DmvcOSihTVOzZ8OveroAIA --profiles` (a `test-update-integrity` marannon task)
- Got: `has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent...`
- The task's artifacts expire in 2027-09. The job never uploads a profile because it is not a test-harness job. The message does mention that case last, but it leads with expiry. Checking the other artifacts' `expires` field would tell the two cases apart.
