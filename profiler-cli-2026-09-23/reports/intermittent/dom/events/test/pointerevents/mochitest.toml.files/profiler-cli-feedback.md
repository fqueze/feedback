## Test `Log` markers print "(empty)" although their message is present

- Command: `profiler-cli thread markers --session mochitest.toml-2 --category Test --list --limit 0` on the `https://example.com` GeckoMain thread of
  `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/d_hHfngtSZuxkpVEwq-Qlw/runs/0/artifacts/public/test_info/profile_test_pointerrawupdate_event_count_touch.html.json`
  (and `marker info m-6`).
- Expected: the INFO lines of the test log, e.g. `SimpleTest START`, `run tests in a new tab`.
- Got: `[(empty)] INFO: (empty)` for every `Log` marker. `marker info m-6 --json` shows `"value": "SimpleTest START"` with `"formattedValue": "(empty)"`, same for `level` (`"value": "INFO"`).
- Workaround: `--json` and read `value`. Another agent's profile of the same test (different task) displayed these messages fine, so it may depend on the profile's marker schema.
