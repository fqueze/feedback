## `thread markers --search` rows do not show what matched

- Command: `profiler-cli thread markers --search restart,reset,NET_RESET,tcp_connection,connection_refused --list --limit 40 --session <id>` on a parent GeckoMain thread.
- Expected: markers whose name or payload contains one of the terms, with enough of the payload printed to see which.
- Got: 4759 rows, mostly `IPCOut` / `IPCIn` / `Runnable PProfiler::Reply_EnsureStarted` with an empty or unrelated description, so no way to tell from the output which field matched (or whether it was a false positive).
- Workaround: searched for the exact Glean metric names instead (`--search handshake_type`, `--search http_channel_disposition`).
- Suggestion: print the matched field (`field=value`) on each row when `--search` is a bare term.
