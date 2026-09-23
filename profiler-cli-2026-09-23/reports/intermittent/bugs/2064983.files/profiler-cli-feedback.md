## `profile meta` shows `Name:  158` for xpcshell profiles

- Command: `profiler-cli profile meta --session 2064983-1` on an xpcshell per-test profile (`profile_test_URIFixup_info.js.json`, task dPBPJm4AQN2X8JrWjXA5Sw).
- Expected: a line that reads as the version, such as `Version: 158` (the product name is empty for xpcshell).
- Got: `Name:  158`, which reads as if the product were named "158". A browser-chrome profile shows `Name: Firefox 158 (build 20260910163201)`.
- Workaround: none needed once you know about it.
