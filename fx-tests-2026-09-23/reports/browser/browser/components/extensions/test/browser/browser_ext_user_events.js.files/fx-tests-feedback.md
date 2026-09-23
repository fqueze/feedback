## `test --bugs` shows no bug although one names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_user_events.js --bugs`
- Expected: bug 1775585, "Intermittent browser/components/extensions/test/browser/browser_ext_user_events.js | single tracking bug" (NEW).
- Got: the normal output, with no bug section and no "no bug found" line.
- Workaround: Bugzilla REST `bug?summary=browser_ext_user_events&resolution=---`.

## `test --task-ids` labels a task with another chunk than `task` does

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task IT0S_eCQRJ-SkMnxZ3h6jA`.
- Expected: the same job name.
- Got: `IT0S_eCQRJ-SkMnxZ3h6jA.0  test-macosx1500-aarch64/opt-mochitest-browser-chrome-1` in the first, `Task IT0S_eCQRJ-SkMnxZ3h6jA.0 — test-macosx1500-aarch64/opt-mochitest-browser-chrome-7` in the second.

## Question: which test failed first in this job?

- Command: `fx-tests task IT0S_eCQRJ-SkMnxZ3h6jA --profiles --limit 0`
- The 49 failing tests are listed alphabetically, so the leaker in a cascade (one test leaves vsync on, every later test fails) is not visible; I had to open a per-test profile and list the `waiting for vsync` markers to see the first timeout. A start time per failing test, or a chronological order, would answer it.
