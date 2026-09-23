## Question: "has this test been failing longer than the window shows?"

- Command: `fx-tests test browser/base/content/test/browser-fullscreen/browser_bug1620341.js --history`
- Expected: a note that the path has no data before 2026-09-11 because the test was moved (bug 1864085's summary still names `browser/base/content/test/fullscreen/`), or a way to follow the old path.
- Got: ten days of `0 pass 0 fail 0 skip`, which reads like a new test or a gap in the data.
- Workaround: inferred the move from the bug summary. Output that would have answered it: "no runs under this path before <date>; runs under <old path> until <date>".
