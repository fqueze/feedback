# fx-tests feedback (browser_fullscreen_window_open.js)

## Question: how often has this test failed over the window, across a directory rename?

- Command: `fx-tests test browser/base/content/test/browser-fullscreen/browser_fullscreen_window_open.js --history`
- Expected: the test's history over the 21-day window, or a note that the path only exists
  since 2026-09-11.
- Got: 0 pass / 0 fail / 0 skip for 2026-09-01 … 2026-09-10, looking like "not run", and
  "Since" was undecidable. The directory had been renamed from `browser/base/content/test/fullscreen/`
  on 2026-09-11; the bug title still names the old path, which is how I found it.
- Workaround: ran the same command on the old path and added the two by hand (75 + 46 failures).
- Could have shown: when a path has no rows at all before a date, a hint of a same-named test
  under another directory whose rows stop on that date.
