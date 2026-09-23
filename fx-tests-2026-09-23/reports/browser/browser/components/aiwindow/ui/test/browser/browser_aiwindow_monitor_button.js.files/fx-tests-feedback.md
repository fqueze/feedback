## Question: "on which configs does failure mode N happen?"

- Command: `fx-tests test <path> --task-ids --issue 2 --limit 0`, then `awk | sort | uniq -c` on the job names, again for `--issue 1`.
- The per-config table mixes all failure modes, so it could not show that mode 1 was Linux/Windows only and mode 2 macOS only. That split was the evidence tying mode 1 to a test skipped on macOS.
- What would have answered it: a per-config breakdown under each Issues row (or `--issue N` also filtering the per-config table).

## Question: "is this failure on a revision that has the fix?"

- `fx-tests test <path> --day 2026-09-18 --task-ids` gave the day each job ran. Those 5 failures turned out to be on a 2026-09-10 mozilla-central push being retriggered. Their day read as "the fix didn't hold" until I looked up the push date on Treeherder.
- What would have answered it: the push date (or revision) next to each task ID, or `--history` counting by push date instead of run date.

## (review) Question: "which macOS configs run this test at all?"

- Command: `fx-tests test <path> --coverage`
- The text output named only the macOS configs that had failures, and "Scheduled on: mac 5/5 ran" with no names. I needed the names to check whether a `skip-if` on `os_version == '15.30' && arch == 'aarch64'` covers every macOS config.
- Workaround: `--coverage --json` and a regex over it for `test-macosx...`.
- What would have answered it: list every config under "Scheduled on", or add a `--configs` flag.
