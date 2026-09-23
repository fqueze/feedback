## `task --messages` lists a TEST-KNOWN-FAIL as a failing message

- Command: `fx-tests task eABqeb6eSdSCS3_hEDsVvw --profiles --messages`
- Expected: only the failing messages under "FAIL — 2 failing executions of 2".
- Got: `2x changed preference: browser.profiles.profile-name.updated` listed first, but in the profile it is a `TEST-KNOWN-FAIL` marker (the harness's pref-leak known-fail), not a failure.
- Workaround: checked the profile's Test markers to see it was a KNOWN-FAIL.
