## Daemon died silently mid-session

- Command: `profiler-cli zoom push 23.0,24.07 --session browser-enable_backup_encryption-1` then `thread markers --category Accessibility --list --limit 0` (profile: W_7L_dzXT6qsEHcfzYTBrw per-test profile, ~7M markers on t-0).
- Expected: the marker list for a 1 s window.
- Got: `Session ... is not reachable. The daemon exited without cleaning up.` The daemon log ends at `Received message: command` with no error. Machine swap was full at the time (possibly OOM kill), but nothing in the log says so.
- Workaround: reload the profile; all m-N handles lost.
