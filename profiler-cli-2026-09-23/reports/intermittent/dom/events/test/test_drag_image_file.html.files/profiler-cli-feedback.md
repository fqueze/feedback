## Log marker text shown as "(empty)" in list and marker info

- Command: `profiler-cli thread markers --session test_drag_image_file.html-1 --thread t-19 --category Test --list --limit 0` (and `profiler-cli marker info m-34941`)
- Expected: the INFO markers' messages, e.g. `add_setup | Entering init`, `DataTransfer number of types: 8`.
- Got: `[(empty)] INFO: (empty)` for every INFO marker of a mochitest-plain profile (Log type markers, Test category, content-process main thread). `--json` shows `"value": "add_setup | Entering init"` with `"formattedValue": "(empty)"`: the formatter drops the string value.
- Workaround: `--list --json` and a python script printing `fields[].value`.
- Question it blocked: "what did the test log (info()) just before the failure" — the most basic question for any test failure.

## Question: "the log of a mochitest-plain test" — not on the parent main thread, and not findable by file name

- Command: `profiler-cli thread markers --session test_drag_image_file.html-1 --thread t-0 --category Test --search test_drag_image_file --list --limit 0`
- Expected: the test's TEST-PASS / INFO / TEST-UNEXPECTED-FAIL markers (as the brief describes for browser-chrome).
- Got: 0 markers. For mochitest-plain they live on the content process main thread (`http://mochi.test`, or `mochi.xorigin-test` for xorig), and only the per-test `test` interval marker names the file; the TEST-*/INFO markers carry no file name, so `--search <file name>` never finds them.
- Workaround: `--category Test` on the content main thread, then `zoom push` from the end of the previous test's `test` marker.
- What would have answered it: `profile markers --search <file>` pointing at the thread, or the `test` marker's range usable as a zoom target for the test that failed (it is only emitted for finished tests, not for the failing one still running).

