## Question: which process keeps vsync enabled?

- Command: `profiler-cli thread markers --session S --search IPCOut --group-by field:messageType`, then `--search 'PVsync::Msg_Notify' --group-by field:otherPid`. The pid then had to be matched by hand to a process in `profile info`.
- Expected: `--group-by field:otherPid` (or a `field:to` grouping) to label each group with the process name as well as the pid, the way `marker info` already prints `sent to WebExtensions (Thread ID: 29401)`.
- Got: bare pids (`2530: 514 markers`).
- Workaround: `marker info` on one example to read the process name.

## Session directory not writable by default in this sandbox

- Command: `profiler-cli load <url> --session <id>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message itself suggests `PROFILER_CLI_SESSION_DIR`, so this cost one round trip. The subagent brief does not mention that variable, and `profile-link.py` reads it too, so every command needs it.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. I also shortened the session name to `portdisc-N` to keep the socket path short.
