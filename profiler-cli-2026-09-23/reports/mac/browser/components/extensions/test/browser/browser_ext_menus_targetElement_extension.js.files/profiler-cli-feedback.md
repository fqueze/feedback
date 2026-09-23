## `field:value` search with the label printed by `marker info` matches nothing

- Command: `profiler-cli thread markers --thread t-0 --search "Type:PBrowser::Msg_UpdateDimensions" --list --limit 0` (and `Task Name:PBrowser::Msg_UpdateDimensions`, `Event Type:contextmenu`)
- Expected: the IPC markers whose `Type` field (as `marker info` prints it) is `PBrowser::Msg_UpdateDimensions`.
- Got: `No markers match the specified filters.`, silently. `marker info` prints field labels ("Type", "Task Name", "Event Type"), and the search wants payload keys, which the text output never shows.
- Workaround: a bare substring search, `--search UpdateDimensions`, which worked. Suggest accepting the printed label as an alias of the key, or warning when a `field:` prefix names no key in any marker of the thread.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <url> --session <id>` without `PROFILER_CLI_SESSION_DIR`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear suggestion to set `PROFILER_CLI_SESSION_DIR`. Cost one retry; the message was good.

## review: the unwritable default session directory hit the reviewer as well

- Command: `profiler-cli load <link> --session review-…-1` without `PROFILER_CLI_SESSION_DIR`
- Got: the same `EPERM` on `/Users/florian/.profiler-cli` as logged above, which cost one more retry. Every sandboxed agent in the fleet runs into this. A writable default such as `$TMPDIR/profiler-cli`, when the home directory cannot be written, would avoid it.
