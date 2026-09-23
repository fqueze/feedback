## Log markers (Test category INFO) print "(empty)" for Level and Message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_clipboard_editor.html-1` (thread t-18, content process of a mochitest-plain profile, task X2Q6wBZBT4C7IoFUgLngfg), then `profiler-cli marker info m-608`.
- Expected: `INFO  Initializing clipboard with "waitForClipboard-known-value-0.75..."...` — the SimpleTest.info() text, which is the test's own log.
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. `marker info --json` shows `value` holds the text, but `formattedValue` is "(empty)" for both fields.
- Workaround: `marker info --json` per marker, or `thread markers --list --json` and read `.fields[].value` with jq.

Note on the above: all four profiles read (tasks X2Q6wBZBT4C7IoFUgLngfg, N4rCoE0cQeaFg3BQivgR1Q x2, X16exxlhQC2Z6ET010JCWA) come from 2026-09-10..09-14, the window between bug 2052468 and bug 2070537 ("Restore StoreName on marker types that lost it when they were migrated to BaseMarkerType"), so the profile's own Log schema may be what is off. Even so, when `formattedValue` is empty and `value` is not, printing `value` would have saved a JSON round trip.
