## Log markers print "(empty)" for their message

- Command: `profiler-cli thread markers --session test_pointerevent_altitude_azimuth_angle.html-1 --thread t-46 --category Test --list --limit 0` (per-test profile of task OcOV8-V4RAi0iY5jT4ZZpw), and `profiler-cli marker info m-307`.
- Expected: `INFO  add_task | Leaving test_pointer_touch_tiltX_tiltY`.
- Got: `INFO  [(empty)] INFO: (empty)` in the list and `Level: (empty)`, `Message: (empty)` in `marker info`. `marker info --json` has the text in `value` but `formattedValue` is `"(empty)"`. Same for the `TEST-UNEXPECTED-FAIL` marker's `Message`.
- Workaround: `marker info <m> --json | rg '"value"'` for each INFO marker. The add_task Entering/Leaving lines are what place the timeout in the test, so the default output hid the key step.

## (review) `--search` misses Log marker messages

- Command: `profiler-cli thread markers --search Entering --list --limit 0 --session review-test_pointerevent_altitude_azimuth_angle.html-1` on t-46 (harness thread, per-test profile of task OcOV8-V4RAi0iY5jT4ZZpw).
- Expected: the `INFO` marker whose `message` is `add_task | Entering test_pointer_touch_altitude_azimuth` (markerIndex 37205).
- Got: `0 markers`. The message text is in the payload `value`, so search seems to match only `formattedValue`, which is `(empty)` (see the entry above).
- Workaround: list `--category Test` in the time range, then `marker info --json` on each INFO marker.
