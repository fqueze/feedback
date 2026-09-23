## `--search file://` matches every IPC marker to or from a file:// process

- Command: `profiler-cli thread markers --session <s> --search file:// --list --limit 50` (on a content process thread, and on the parent main thread)
- Expected: markers whose name or text contains `file://` (a URL in a navigation or load marker).
- Got: 370 and 358 hits, almost all `IPCIn` / `IPCOut`, apparently because their payload carries the peer process name `file:// Content (2/2)`. The URL I was looking for came up only through `--search tmp<profile dir name>` and `--search neterror`.
- Workaround: search for a more specific substring of the URL, or exclude with `-name:IPCIn,-name:IPCOut`.
