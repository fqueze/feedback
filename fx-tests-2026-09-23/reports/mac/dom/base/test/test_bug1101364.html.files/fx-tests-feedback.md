# fx-tests feedback — test_bug1101364.html

## `fx-tests test` hides the failure message that `fx-tests task` has

- Command: `fx-tests test dom/base/test/test_bug1101364.html`
- Expected: the Issues row to carry the failure message of the 21 failing runs (`Test timed out.`,
  followed by `[SimpleTest.finish()] No checks actually run.`).
- Got: `21x FAIL Failure details not recorded (likely Android or platform logging issue)`, on a
  macOS config. `fx-tests task <id>` for each of those jobs has the message
  (`[SimpleTest.finish()] No checks actually run...`), and the resource-usage profile has the
  first one, `Test timed out.`.
- Workaround: `fx-tests task <id>` on every task id. The row made the failure look unknowable and
  hid that it is a 300 s timeout.

## `fx-tests test --bugs` prints nothing when no bug is found

- Command: `fx-tests test dom/base/test/test_bug1101364.html --bugs`
- Expected: a `Bugs` block, or a line saying no sheriff-annotated bug names the test.
- Got: the ordinary output, no mention of bugs at all, so "none found" and "lookup silently
  failed" look the same.

## Question needing a script: "in which jobs did test A run in the same chunk as test B, and did A fail there?"

- Commands: `fx-tests test <other test> --task-ids --limit 0 --config macosx1500-aarch64/opt`
  to get a population of jobs of the chunk, then `fx-tests task <id> --json --passed` per job
  (hundreds of calls, ~5 s each) with a Python filter for whether
  `dom/base/test/fullscreen/*` and `dom/base/test/test_bug1101364.html` both ran, and whether
  the latter failed.
- What the output could have shown: `fx-tests test <path> --coverage` or `--executions` could list,
  per failing and per passing job, the manifest that ran just before the test's manifest (the
  resource-usage profile has `Running manifest:` markers). That is the order-dependency question
  the brief asks, and there is no way to get the passing jobs' task ids except through another
  test's failures.
