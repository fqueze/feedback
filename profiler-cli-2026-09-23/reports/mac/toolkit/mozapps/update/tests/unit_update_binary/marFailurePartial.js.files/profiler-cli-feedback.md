## Daemon dies silently while loading a large per-test profile

- Command: `PROFILER_CLI_SESSION_DIR=... PROFILER_CLI_SESSION_OWNER=marFailurePartial.js profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/RYaZzv7pRq-Tdo2ShDkBEg/runs/0/artifacts/public/test_info/profile_marFailurePartial.js.json --session marFailurePartial.js-1` (60 MB gzipped, ~4.8M markers, machine at ~4 GB free)
- Expected: the profile loads, or an error saying why it could not (out of memory, killed, parse failure).
- Got: first attempt printed `Error: Session marFailurePartial.js-1 is not reachable ... The daemon exited without cleaning up.`; second attempt printed only `Loading profile from ...` and exited 0 with no session. The daemon log ends at `Fetching profile from ...` both times, with no error line.
- Workaround: picked the job's smaller profiles (25-28 MB gzipped), which loaded in 12 s. Cost two load attempts and a detour.
