## Question: which document is this marker about

- Command: `profiler-cli marker info m-124 --session …` shows `Other payload fields (no schema): innerWindowID: 24` on `RefreshObserver` markers; `profiler-cli thread markers --search "innerWindowID:24"` matches nothing, and no command lists the profile's `pages` (innerWindowID → URL).
- Expected: to tell which of three `Accessibility notifications` refresh observers belonged to about:preferences, browser.xhtml, or the harness window.
- Workaround: downloaded the profile with curl and read `pages` in Python (innerWindowID 2 = browser.xhtml, 24 = browser-harness.xhtml, 49 = the test's about:preferences tab).
- What could have shown it: `profile info`/`profile meta` listing pages, or `marker info` resolving `innerWindowID` to its URL; and `--search` accepting non-schema payload fields.
