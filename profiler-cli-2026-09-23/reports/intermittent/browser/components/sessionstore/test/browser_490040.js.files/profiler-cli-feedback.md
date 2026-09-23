## `thread markers --search "TEST-"` matches Preference Read payloads

- Command: `profiler-cli thread markers --search "browser_490040,SSWindowRestor,...,TEST-" --list --limit 0 --session browser_490040.js-2`
- Expected: the harness's `TEST-PASS` / `TEST-UNEXPECTED-FAIL` markers.
- Got: dozens of `Preference Read` markers first (`dom.origin-trials.test-key.enabled`, `urlclassifier.*: moztest-...`), because the search is case-insensitive and also matches payload values. The harness markers were buried in them.
- Workaround: `| rg -v "Preference Read"`, or search on the message text (`That window`, `Test timed`) instead. A case-sensitive option, or a way to leave out one marker name (`-name:` also matches Text markers' `name` payload), would have avoided the rerun.

## (review) `--search` does not match a marker's text as the list prints it

- Command: `profiler-cli profile markers --search "[BrowserTestUtils] load" --session review-browser_490040.js-1` (also `--search "] load"`)
- Expected: the `SendAsyncMessage` / `ReceiveMessage` rows that the list prints as `[BrowserTestUtils] load`, to see when the content process sent the load.
- Got: `No markers match the specified filters (searched 77 threads)`. The printed text is built from two payload fields (`actor: BrowserTestUtils`, `name: load`), and the search matches one field at a time, so text copied from a row finds nothing, with no hint why.
- Workaround: `--search BrowserTestUtils`, then read the rows. Matching the printed description too, or saying in the no-match message that the term spans fields, would have avoided the retry.
