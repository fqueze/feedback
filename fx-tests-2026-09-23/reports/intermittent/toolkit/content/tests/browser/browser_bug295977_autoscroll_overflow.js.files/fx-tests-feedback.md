## Question: "which revision was the last failing / first passing run on this config?" (to find the landing that stopped a failure)

- Command: `fx-tests test <path> --history --config test-windows11-64-25h2-ccov/opt-mochitest-browser-chrome`
- Expected: a way to get the revisions (or task ids) of the *passing* runs around a step change, e.g. `--task-ids` including passes, or a per-day revision range in `--history`.
- Got: per-day pass/fail counts only; `--task-ids` lists failing tasks only. The step change was clear (60% fail until 2026-09-09, 0/41 after), but bounding it to a push needed Treeherder API queries per push (and the Treeherder jobs endpoint caps at 2000 rows per push, so even that was incomplete).
- Workaround: took the last failing task's revision from `fx-tests task`, then listed hg pushes after it and grepped their changesets for autoscroll/APZ files.
- What the output could have shown: first passing revision per config after the last failure (`Since` in a "no longer fails" report needs exactly this).

## Question: "which execution is this per-test profile from, the first or the harness retry?" (review)

- Command: `fx-tests task PeSVpa3RQIu3DuBLiDreLA --profiles`
- Expected: with "FAIL — 2 failing executions of 2", either two profiles (`…js.json` and `…js-2.json`) or a note saying which execution the single listed profile belongs to.
- Got: one profile URL, no execution label. The report under review called it "first attempt" in one place and "retry" in another.
- Workaround: the profile's test starts 0.45 s after "Start BrowserChrome Test Results" in a fresh browser, and the manifest lists 8 tests before it, so it is the retry (the first execution's profile was presumably overwritten under the same name).
- What the output could have shown: per profile, the execution it came from (first / retry) and whether a profile for the other execution was expected but missing.
