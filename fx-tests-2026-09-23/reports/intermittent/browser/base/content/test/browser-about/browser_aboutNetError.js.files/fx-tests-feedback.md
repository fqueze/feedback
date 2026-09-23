## `fx-tests test` does not follow a test across a directory rename

- Command: `fx-tests test browser/base/content/test/browser-about/browser_aboutNetError.js --history`
- Expected: the test's 21-day history, or at least a note that it has runs under a previous path (it was `browser/base/content/test/about/` until bug 2069131 landed on 2026-09-16/17).
- Got: 5 days of data, 1,140 runs and 8 failures, with the first 15 days showing zero runs, which reads like a brand-new test. The old path held 8,740 runs and 87 failures, including 37 of the 45 failures of the modes I diagnosed.
- Workaround: I noticed the old path in the bug summary and re-ran `fx-tests test` on it, then summed the two by hand. The question was "how often has this test failed over the window". A line such as "no runs before 2026-09-16; same file name ran under browser/base/content/test/about/ until 2026-09-17" would have answered it.
