## `thread markers --search` cannot match a pid as the marker text renders it

- Command: `profiler-cli thread markers --session browser.toml-1 --search "Child 7,232" --list --limit 0` (resource-usage profile of task LiEnGsrrQvuzs5kAvA6WzQ)
- Expected: the DocShell/DOMWindow/assertion markers of content process 7232. Their text renders as `[Child 7,232: Main Thread]`.
- Got: 441 unrelated markers (CPU Use, Memory, IO...), because the comma splits the term into `Child 7` OR `232`. Searching `7232` finds nothing, since the rendered text has a thousands separator.
- Workaround: search for the docshell or window address from the log (`19257c72800`), or for a message unique to that process.
- Suggestion: don't insert thousands separators into pids in marker text/fields, or support quoting or escaping a comma in `--search`.
