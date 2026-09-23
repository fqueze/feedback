# fx-tests feedback (browser_preferences.js)

## `task --messages` omits messages that `test --issue` attributes to the task

- Command: `fx-tests task dIgh7VEYQDO8Su2EyF7zkA --profiles --messages`
- Expected: `fx-tests test browser/components/profiles/tests/browser/browser_preferences.js --task-ids --issue 2` lists this task under issue 2, "uncaught rejection: profileList.value.map is not a function", so I expected that message (and the copyProfile `profilesSelect is null` failure before it) in the task's messages.
- Got: only 3 messages: `changed preference: identity.fxaccounts.account.device.name`, `Found an unexpected tab at the end of test run: about:blank`, `The profiles note is hidden`. The profile shows `TEST-UNEXPECTED-FAIL Uncaught exception in test - TypeError: can't access property "value", profilesSelect is null` and `uncaught rejection: profileList.value.map is not a function` at t=42.223.
- Same the other way for Hr5-JVV1TEqlZnkJI5BICw: `--issue 3` files it under "changed preference" (a TEST-KNOWN-FAIL), while `task --messages` shows `Both profiles and the placeholder ... 2 == 3` for it.
- Workaround: loaded the profile and read the Test markers.

## Question: which profiles hold a *passing* run of my test?

- Question: "a profile in which this test passed, to compare against the failing ones".
- Command I ended up using: `fx-tests test <each later test in the same manifest> --task-ids`, then `fx-tests task <id> --profiles` on a hit (browser_window_title_test.js failing in N8R5K4bWSxSUQGzna_anjw, whose per-test profile covers browser_preferences.js passing earlier in the same browser).
- What could show it: `fx-tests test <path> --passing-profiles` listing failure profiles of tests that ran after this one in the same browser (same manifest, same job) while this one passed.
