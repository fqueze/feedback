## Question: what did the test `info()` at each step?

- Command: `profiler-cli thread markers --session test_image_recognition_unsupported.html-1 --category Test --list --limit 0` (thread t-12, mochitest-plain content process), then `profiler-cli marker info m-90 m-91`
- Expected: the text of each `INFO` line of the test log, e.g. `add_task | Entering`, `Recognizing the current image text is not supported on this platform.`
- Got: every `Log` marker printed as `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. The `--json` output has the text in `fields[].value` but `formattedValue` is `"(empty)"` for both fields.
- Workaround: `marker info <handles> --json` and read `fields[].value`. The formatter for the `Log` marker type seems to ignore the value; the default output could have shown `value` whenever `formattedValue` is empty.
- Also: the default session directory `/Users/florian/.profiler-cli` is not writable in the sandbox; the error message named `PROFILER_CLI_SESSION_DIR`, which worked.

## Question: is the marker I found the one a link's `marker=N` points at? (review-test_image_recognition_unsupported.html)

- Command: `profiler-cli marker info m-22 --session review-test_image_recognition_unsupported.html-1`
- Expected: the marker's index in its thread, e.g. `Index: 17309`, so it can be checked against a profiler.firefox.com link's `marker=17309`.
- Got: type, category, time, thread, fields and stack, but no index. It is only in `--json` as `markerIndex`, so checking a dozen links took a script over `marker info <handles> --json`.
- Workaround: `marker info m-a m-b … --json` and print `markerHandle`, `markerIndex`. The default output could show the index next to the handle, and `thread markers --list` could have an optional index column.
