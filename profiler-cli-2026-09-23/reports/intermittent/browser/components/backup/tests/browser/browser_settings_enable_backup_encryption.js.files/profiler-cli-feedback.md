## `load` selected the WebExtensions main thread, not the parent's

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/ZaWxp-3VQLCG9SzcXnN5mQ/runs/0/artifacts/public/test_info/profile_browser_settings_enable_backup_encryption-2.js.json" --session <s>` then `thread markers --search ... --list`
- Expected: t-0 (GeckoMain, Parent Process) selected, as with the other per-test profiles I loaded.
- Got: t-14 (GeckoMain, WebExtensions) selected; my first marker query silently returned the WebExtensions thread's a11y markers, which looked plausible (DoInitialUpdate, nsAccessibilityService::Init) and could have been mistaken for the parent's.
- Workaround: `thread select t-0` after every load.
