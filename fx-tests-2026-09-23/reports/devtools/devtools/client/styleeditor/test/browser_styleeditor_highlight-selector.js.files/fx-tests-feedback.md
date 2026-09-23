## Question: "did a tab crash during this failing test?" (2026-09-22)

- `fx-tests test devtools/client/styleeditor/test/browser_styleeditor_highlight-selector.js` and `fx-tests task ESeP1TF4SpyWmFfOAETZGw --profiles`
- Expected: some hint that the one TIMEOUT came with a content-process death (the job log has `console.error:` / `remote browser crashed while on` / `about:blank` for this test, and the parent profile has `oop-frameloader-crashed`).
- Got: only `TIMEOUT Test exceeded time limit`, 0 crash. No minidump existed (every child in the job logs "could not rendez-vous with the crash helper"), so the crash counters cannot see it.
- Then `fx-tests errors --day 2026-09-11 --message "remote browser crashed"`: "No markers matched". The console.error is multi-line and its first line is empty, so the text that identifies it is in the following lines, which the errors data does not keep.
- Workaround: found `oop-frameloader-crashed` in the per-test profile, then the job log. Could have shown: a per-failure flag when the failing test's log has "remote browser crashed" (or the errors data keeping multi-line console.error bodies), so "how many tests / jobs had a tab crash" is answerable tree-wide.
