## Sample search misses samples in the unzoomed full view

- Command (session on https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/B5XIaXu4QYqi9Xa--D4LbQ/runs/0/artifacts/public/test_info/profile_browser_settings_create_backup.js.json, thread t-0, no zoom, no filters):
  `profiler-cli thread samples-top-down --search onIdle` and `--includes-function f-6499`, also `profiler-cli function info f-6499`
- Expected: the one sample at t=39.398s whose stack contains `BackupService.sys.mjs!onIdle`.
- Got: "No samples matched --search "onIdle"" / "No samples for this function on t-0 in the current view". After any explicit `zoom push` (`0,47.011`, `0,40`, `30,47.011`, `26.4,46.5`) the same command finds the sample. `thread functions --search BackupService` inside a zoom listed onIdle with total 1, which is what made me look.
- Workaround: always `zoom push 0,<end>` before sample searches. Cost: a wrong "never called" conclusion is easy to draw from the full view.
