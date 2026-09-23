## `field:value` search does not match a raw-payload field that `--group-by field:` can read

- Command: `profiler-cli thread markers --session <s> --search "innerWindowID:4294967338" --list` (and `"innerWindowID:4294967331,-name:RefreshDriverTick waiting for paint"`)
- Expected: every marker carrying that innerWindowID, such as the DocumentLoad and nsRefreshDriver markers of that window.
- Got: `No markers match the specified filters.` Yet `--group-by field:innerWindowID` on the same thread groups by that field, and `marker info` shows it under "Fields (raw payload, no schema)".
- Question I was answering: "which document is this stuck refresh driver's window". The answer came from running `marker info` on the DocumentLoad markers one by one until I found the matching innerWindowID.
- Workaround: `--search name:DocumentLoad` in a zoomed range, then `marker info` on each candidate.

## Default session dir not writable in the sandbox; the error is clear but only shows at load

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a suggestion to set PROFILER_CLI_SESSION_DIR. Setting it to `/Users/florian/.sandbox/tmp/profiler-cli` worked.
- Suggestion: fall back to `$TMPDIR` when the default dir is not writable. Otherwise every agent loses one load finding this out.
