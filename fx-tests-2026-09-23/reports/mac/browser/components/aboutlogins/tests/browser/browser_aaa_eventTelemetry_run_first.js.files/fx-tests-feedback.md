## `task --messages` lists a TEST-KNOWN-FAIL as a failure message

- Command: `fx-tests task eRaVbxPOR3qyIbV0Ht4Obw --messages`
- Expected: only the messages that made the test fail (TEST-UNEXPECTED-*), or known fails marked as such.
- Got: under `FAIL — 2 failing executions of 2`, the line `2x changed preference: toolkit.osKeyStore.unofficialBuildOnlyLogin` next to the real failure, with nothing saying it is a TEST-KNOWN-FAIL (the per-test profile's parent main thread has it as a `TEST-KNOWN-FAIL` marker from `comparePrefsToBaseline`).
- Cost: it reads as a second failure (a pref leak), and I had to open the profile to see that it is not one.
- Workaround: `profiler-cli thread markers --category Test --list` on the per-test profile, which gives the status of each message.
