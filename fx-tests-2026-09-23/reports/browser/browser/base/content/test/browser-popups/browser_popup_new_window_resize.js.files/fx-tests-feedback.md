## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/base/content/test/popups/browser_popup_new_window_resize.js --bugs`
- Expected: a "Bugs" section, or a line saying no sheriff-annotated bug names this test.
- Got: the normal output with no bugs section at all, so "no bug" looks the same as "the lookup silently did nothing".
- Workaround: assumed no bug.

## Question: which pushes ran this test and passed, either side of a step change?

- Question: the test stopped failing after 2026-09-10 (16 fails in ~118 runs, then 0 in ~123). To look for the landing that stopped it I needed the revisions of the last failing run and of the first passing runs after it on the failing config.
- Command: `fx-tests test <path> --config windows11-64-25h2/debug-mochitest-browser-chrome-standalone --history` gives per-day counts only; `--task-ids` lists failing tasks only; `--json` has no per-run revisions.
- What could have shown it: `--history` (or a `--runs` flag) listing, per day, the push revisions that ran the test on that config with pass/fail, so the boundary is a revision range rather than a day.
- Workaround: queried the Treeherder push/jobs API by hand for m-c pushes on 2026-09-08…12; that still cannot say which chunk ran the test, so the boundary stayed at day granularity.
