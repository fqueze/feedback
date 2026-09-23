## `task --messages` omits a message that `test --issue` attributes to that task

- Commands: `fx-tests test browser/components/profiles/tests/browser/browser_preferences.js --task-ids --limit 0 --issue 2` lists `dIgh7VEYQDO8Su2EyF7zkA.0` for "uncaught rejection: profileList.value.map is not a function"; `fx-tests task dIgh7VEYQDO8Su2EyF7zkA --messages --full-messages` then lists only "changed preference: identity.fxaccounts.account.device.name", "Found an unexpected tab at the end of test run: about:blank" and "The profiles note is hidden" for that test.
- Expected: the task view to list the uncaught rejection (and the "profilesSelect is null" exception), which are in the job's per-test profile as TEST-UNEXPECTED-FAIL at t=42.223 / 42.224.
- Got: neither message; I had to load the profile to find which failure mode this task was.
- Workaround: load the per-test profile and read its Test markers.
