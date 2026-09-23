## Log markers (INFO) print "(empty)" for Level and Message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` and `profiler-cli marker info m-67 --session <s>` on the content-process GeckoMain of a mochitest-plain per-test profile (task H7LZmKs2T1KWqmTL-G-H8w, profile_test_multi_locale_CA_address_form.html.json).
- Expected: `INFO  expecting a popup` (the test's `info()` log line).
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in marker info. `--json` shows `"value": "expecting a popup"` with `"formattedValue": "(empty)"`.
- Workaround: loop `marker info <m> --json` over every INFO handle and print `fields[].value`. This is the test's own log, so it costs one call per line.

## `--search` commas mean OR, AND, or nothing depending on the command (review)

- Command: `profiler-cli thread functions --session <s> --thread t-0 --limit 0 --search 'Gray,collectSlice,BeginCollection,HeapCheck'` (task H7LZmKs2T1KWqmTL-G-H8w, zoomed on the parent CC slice).
- Expected: the functions matching any of the terms, as `thread markers --search a,b` does (OR).
- Got: `No functions match the specified filters.` `thread functions --search` is a plain substring, so the comma is literal; `thread samples --search a,b` ANDs the terms. Nothing in the output says the comma was taken literally.
- Workaround: one call per term.
