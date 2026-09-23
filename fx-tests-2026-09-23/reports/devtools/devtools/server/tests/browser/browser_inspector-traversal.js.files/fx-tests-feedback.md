## Question: does this job have a shutdown profile, and where?

- Command: `fx-tests task ARUp7AUORdi6qzwpfDkKDA --profiles`
- Expected: the job's other profiles listed too; this job also uploaded `profile_devtools_server_tests_browser_browser_shutdown.json` (346 MB), tied to an AsyncShutdown timeout caused by the same hung process.
- Got: only the per-test failure profile and the resource-usage profile; I found the shutdown profile only from an `artifact` marker in the resource-usage profile.
- Could have shown: every `profile_*.json` artifact of the job, with the failure line it belongs to.
