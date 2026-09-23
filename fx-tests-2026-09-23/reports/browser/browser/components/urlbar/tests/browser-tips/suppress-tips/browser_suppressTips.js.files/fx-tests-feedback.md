## `task --profiles`: which execution a per-test profile comes from

- Command: `fx-tests task KBQXJ3tLS6yUVak-6zP6VA --profiles --messages`
- Expected: with "FAIL — 2 failing executions of 2" and a single profile listed, some way to tell whether that profile is from the first run or the harness retry.
- Got: one `profile_browser_suppressTips.js.json`, no label. It is from the **retry**. The first run left no per-test profile at all, because its only unexpected failure (`leaked 1 window(s) until shutdown`) is found by the leak parser after the browser exits. Following the brief's rule ("one profile = first run, `-2` = retry") I would have read the retry's fallout as the primary failure.
- Workaround: the resource-usage profile's `test` markers (first run `m-3`, `retry` `m-136`, retry `m-2`) and the `profile uploaded` / `artifact` markers, which sit inside the retry.
- Suggestion: tag each profile with the execution it belongs to, and say "no profile for the first run" when one is missing.

## `--messages` lists a todo as a failure message

- Command: `fx-tests task <id> --profiles --messages` (and `fx-tests test <path>` Issues)
- Got: `changed preference: browser.laterrun.bookkeeping.updateAppliedTime` is listed among the failing messages. In the run it is a todo (expected FAIL, since `comparePrefs` is off): the harness summary says "Failed: 0, Todo: 1".
- Suggestion: leave out expected-FAIL (todo) results, or mark them as known failures.
