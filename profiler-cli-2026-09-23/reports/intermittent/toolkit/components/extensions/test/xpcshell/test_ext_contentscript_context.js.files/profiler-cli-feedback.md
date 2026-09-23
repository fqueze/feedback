## Loading a large xpcshell per-test profile kills the daemon with SIGABRT

- Command: `PROFILER_CLI_SESSION_OWNER=test_ext_contentscript_context.js profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/RnZdOuVXTtiSMYCz9Lkmow/runs/0/artifacts/public/test_info/profile_test_ext_contentscript_context.js.json --session test_ext_contentscript_context.js-2`
- Expected: the profile loads, or an error saying it is too large and what to do.
- Got: first try, the daemon died silently during "Fetching profile" (the next command only said the socket was not reachable, the log ended at "Fetching profile"). Second try: "The profiler-cli daemon died while loading the profile (killed by signal SIGABRT)". The profile is 50 MB gzipped / about 870 MB of JSON: a macOS xpcshell parent main thread holds millions of `Runnable DummyEvent` + `TaskController::AddTask` markers (nsBaseAppShell dispatches one per event loop turn, about 25,000-40,000/s). A 22 MB gzipped profile of the same test (AR9_lwuqQy6yF86T9hpJlA, 4.1 M markers on t-0) already used 2.1 GB RSS, far above the 0.6 GB per session the fleet is sized on.
- Workaround: none tried (did not want to raise the node heap on a shared machine); fell back to the job log for the opt runs.
- Would help: say "out of memory" instead of SIGABRT, or drop/aggregate `DummyEvent` Runnable markers at load time.
