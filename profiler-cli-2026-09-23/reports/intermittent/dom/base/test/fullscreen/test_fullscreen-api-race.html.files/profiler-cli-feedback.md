## Log marker messages shown as "(empty)"

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_fullscreen-api-race.html-1` (thread t-10, content process of a mochitest-plain per-test profile, task Puh1BpkdTeC0Tpq7kjIl2Q, `profile_test_fullscreen-api-race.html.json`), and `marker info m-41`.
- Expected: the `INFO` rows to show their message, e.g. `must wait for focus`, `Testing openNewTab, navigate`.
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)`, `Message: (empty)` in `marker info`. `marker info --json` has `"value": "must wait for focus"` with `"formattedValue": "(empty)"` for the same field, so the formatter drops a value that is there.
- Workaround: `marker info <m> --json` for each INFO marker, or read the task's live log. This is the test's own log (the brief says to start from it), so every INFO line costs a JSON round trip.
