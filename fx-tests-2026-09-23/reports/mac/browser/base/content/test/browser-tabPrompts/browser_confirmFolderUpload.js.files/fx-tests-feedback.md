## The failures of a test that was renamed inside the window

- Command: `fx-tests test browser/base/content/test/browser-tabPrompts/browser_confirmFolderUpload.js`
- Expected: the test's failures in the window, including the ones recorded under its previous path `browser/base/content/test/tabPrompts/browser_confirmFolderUpload.js` (renamed by bug 2069131 around 2026-09-16), or at least a note that an older path has history.
- Got: "Verdict: passing. 1,130 of 1,130 runs passed.", and `--history` shows zero runs before 2026-09-16, with no mention of the old path. The 5 failures and their task IDs only show up when the old path is queried.
- Workaround: found the old path in the list builder's rename map, then ran every command on the old path.
- What the output could have shown: an "also known as" line (or folded counts) when a rename is known, since the default output makes a failing test look healthy.
