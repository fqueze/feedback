## `fx-tests test` Issues block names an INFO line as the failure

- Command: `fx-tests test dom/media/webspeech/recognition/test/test_parakeet_endpoint_blank.html`
- Expected: Issues row 2 to show the failing assertion, e.g. `The clip's two sentences arrive as two final results. Got 3: ... - got 3, expected 2`.
- Got: `74x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: ...` — the `requestFlakyTimeout` INFO message, which every run prints, including passing ones. The real failure modes (Got 3 / Got 1 / "second final carries the second sentence") and their counts are invisible; `--issue`-based grouping by failure mode is therefore useless for this test.
- Workaround: `fx-tests task <id> --messages` per task, one at a time.

## Question: "which failure modes does this test have, and how many runs each?"

- Commands: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --messages` for each of the 57 tasks, scraped with awk and tallied.
- Expected: the per-test Issues block (or a `--messages` flag on `fx-tests test`) to list each distinct failing assertion with its count and configs: here 68x `... Got 3: "... |  I"` (Windows+Mac), 4x `The second final carries the second sentence. Got: " Packbox"` and 2x `... Got 1: ...` (both Linux only).
- Got: only the INFO line (previous entry), so answering it took 57 `fx-tests task` calls.
