## Question: "which manifest variant did each failure run in?" (xpcshell.toml vs xpcshell-remote.toml)

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_dnr_download.js --history --config test-macosx1015-64-qr/debug-xpcshell` (and `--json`)
- Expected: the failures/runs split by manifest (the test runs from two manifests with different prefs: in-process vs remote extensions).
- Got: one merged row per day. The macOS crashes all came from the `xpcshell.toml` copy, which stopped running on desktop on 2026-09-17 (SKIP with `run-if: (os == 'android') && (os != 'android')`); from the aggregates this looked like an unexplained stop.
- Workaround: loaded three resource-usage profiles and read the `test` markers' manifest prefix.

## Question: "the per-test profile URLs of this test's FAIL/TIMEOUT occurrences"

- Command: `fx-tests test <path> --profiles --limit 0`
- Expected: for each failing task, the per-test profile URL (`profile_test_ext_dnr_download.js.json`) when one was uploaded.
- Got: only the `resource-usage:` URL for every task, FAIL and TIMEOUT tasks included.
- Workaround: `fx-tests task <taskId> --profiles --limit 0 --messages | grep -A8 <test>` per task, which does print the per-test profile URL.

## `failures --message` does not find a crash signature

- Command: `fx-tests failures --message "child process hang at shutdown" --tests`
- Expected: the tests behind the top xpcshell crash signature, or a hint that crash signatures live under `crashes --signature`.
- Got: "No failure matched. Searched 4,982 tests ..." — reads as "nobody has this", while `fx-tests crashes` shows 19,349 crashes in 503 tests.
- Workaround: `fx-tests crashes --signature 'child process hang' --day <d>`.
