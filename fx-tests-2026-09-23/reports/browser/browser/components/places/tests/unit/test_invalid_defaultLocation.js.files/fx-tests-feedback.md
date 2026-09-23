## `errors --message` with the literal number from the log matches nothing

- Command: `fx-tests errors --harness xpcshell --message "Error:-2147009284"` (the text of a `C++ warning` "Failed to launch tab subprocess @CreateProcess (Error:-2147009284)" seen in the resource-usage profile of ZgUlY0ZlRvmHZNTOfia3AA, 2026-09-21, the errors file's own date).
- Expected: the 75 occurrences in 19 tests that exist in that file.
- Got: "No markers matched. … this is a filter with no matches rather than an empty file." The messages are stored normalized (`Error:-<num>`), and the query is not normalized the same way, so a query copied from a log silently finds nothing — while the tool asserts the absence is real.
- Workaround: `--message "Failed to launch"`, which shows the normalized form.
- Suggestion: normalize the query with the same rules before matching (or match both), or when nothing matches and the query contains a number, say that numbers are stored as `<num>`.
