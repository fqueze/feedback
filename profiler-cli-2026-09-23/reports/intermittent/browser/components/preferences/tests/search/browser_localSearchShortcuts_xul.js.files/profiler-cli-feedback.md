## `--search` does not match the text the marker list displays (review-browser_localSearchShortcuts_xul.js)

- Command: `profiler-cli thread markers --session <id> --search 'click - ' --list --limit 0`
- Expected: the DOMEvent markers whose listed description is `click - treechildren@… id="engineChildren"` (the same list shows that text).
- Got: `0 markers (filtered from 89273)`. `--search click` matched them, and so did `--search DOMEvent` piped through `rg ' (click|dblclick|command) - '`.
- Workaround: search on one word and filter the output with rg. `--search` could match the rendered description, or `--help` could say which fields it searches.
