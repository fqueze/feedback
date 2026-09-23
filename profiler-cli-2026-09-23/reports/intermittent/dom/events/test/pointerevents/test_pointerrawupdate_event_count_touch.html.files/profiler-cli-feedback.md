## Log (INFO) markers print "(empty)" for Level and Message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on a content-process GeckoMain of a mochitest-plain per-test profile (task MU7tpm5YQ8KjiGBR6h9D1A, profile_test_pointerrawupdate_event_count_touch.html.json, thread t-11); also `marker info m-972`.
- Expected: `INFO  run tests in a new tab`.
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)`, `Message: (empty)` in `marker info`. `marker info --json` shows `"value": "run tests in a new tab"` with `"formattedValue": "(empty)"` — the formatter drops the value.
- Workaround: `thread markers ... --list --json` and read `.fields[].value`.
