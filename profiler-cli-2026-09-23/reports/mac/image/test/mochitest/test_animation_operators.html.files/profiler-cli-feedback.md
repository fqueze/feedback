## Log markers (INFO) print "(empty)" for Level and Message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_animation_operators.html-1` on thread t-19 (mochi.xorigin-test GeckoMain) of `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/ZIeDiAGHTnqSRoAZDFBE0g/runs/0/artifacts/public/test_info/profile_test_animation_operators.html.json`, and `profiler-cli marker info m-599`.
- Expected: the INFO line's text, e.g. `Giving up after 101 tries, maxp=10fact=10`.
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. The `--json` output has `"value": "Giving up after 101 tries, maxp=10fact=10"` with `"formattedValue": "(empty)"` for the same field — the formatter drops the value (field type probably `unique-string` or similar not handled for Log markers).
- Workaround: `marker info <m> --json` and read `fields[].value`. The question it could not answer: what the test logged just before failing.
