## Daemon died without a message

- Command: `PROFILER_CLI_SESSION_OWNER=2060167 profiler-cli thread info --thread t-4 --session 2060167-1`. The session had a 1m24s profile loaded and had just answered `zoom pop` and `thread list`.
- Expected: thread info.
- Got: no output. Every later command reported `Session 2060167-1 is not reachable ... The daemon exited without cleaning up`. The daemon log ends at `Received message: command` with no error. Swap on the machine was full (7/7 GB), so an OOM kill is possible, but nothing said so.
- Workaround: reload the profile. The same command then worked. It cost a reload, and every m-N handle changed, so all later links had to be regenerated.
