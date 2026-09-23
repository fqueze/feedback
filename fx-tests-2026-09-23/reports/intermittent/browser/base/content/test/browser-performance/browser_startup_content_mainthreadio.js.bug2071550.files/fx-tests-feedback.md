## `task --profiles` misses a profile the test uploads itself

- Command: `fx-tests task ERphleY_RUa3yTz_rZEyQw --profiles`
- Expected: to be pointed at the failure's profile. The failure message itself says "open the profile_startup_content_mainthreadio.json artifact", and `public/test_info/profile_startup_content_mainthreadio.json` exists in the task's artifacts.
- Got: "No failing test named a per-test profile in this job."
- Workaround: listed the task artifacts with curl. Suggestion: list every `public/test_info/profile_*.json` artifact that is not the resource-usage one. Note that a test's own artifact is overwritten by the retry, so it comes from the last run.

## A renamed test's history is split across two paths with no hint

- Command: `fx-tests test browser/base/content/test/browser-performance/browser_startup_content_mainthreadio.js --history`
- Expected: the test's history, or a note that the test was at `browser/base/content/test/performance/` until 2026-09-16.
- Got: zero runs until 2026-09-16, which reads as "new test", and 38 failures instead of 136. The step change on 2026-09-10 is only visible under the old path.
- Workaround: queried the old path separately. A heuristic would help: same file name, and the old path stops the day the new one starts.
