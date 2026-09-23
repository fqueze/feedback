## review-test_backgroundupdate_exitcodes.js: daemon dies with SIGABRT, no message, on a 897 MB profile

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/PCjEYf6sSMGS0V5aQczQKQ/runs/0/artifacts/public/test_info/profile_test_crashreporter_crash.js.json" --session review-test_backgroundupdate_exitcodes.js-2`
- Expected: the profile loaded, or an error saying it is too large (the artifact is 897,279,939 bytes, uncompressed JSON).
- Got: first try, "Session ... is not reachable ... The daemon exited without cleaning up"; second try, "The profiler-cli daemon died while loading the profile (killed by signal SIGABRT)", with "Last lines of <log>:" followed by nothing. The daemon log has only the "Fetching profile" line and status polling. 74 GB of RAM were available.
- Workaround: none; read the job's resource-usage profile instead. A size check before fetching, or the V8 error (likely a string/heap limit) in the message, would have saved two loads.
